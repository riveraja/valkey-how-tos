# Using Envoy Proxy for Data Sharding in Redis/Valkey

Envoy is a Layer7 proxy and communication bus designed for large modern service oriented architectures. For additional information about Envoy, please read their documentation [here](https://www.envoyproxy.io/docs/envoy/latest/intro/what_is_envoy).

## Use cases

Envoy can be used for the following use cases:

- Data sharding
- Backend routing with prefix keys
- Load balancing of reads
- Connection Rate Limiter
- Request Mirroring

## Data sharding using Envoy Proxy

In this article, we are going to setup Envoy Proxy for data sharding. We will be using the ring_hash algorithm for [consistent hashing](https://en.wikipedia.org/wiki/Consistent_hashing).

## The setup

We have a redis client that we'll run the `redis-data-generator` script. We have a single node Envoy Proxy to handle sharding of data to the five backend redis servers.

## Installation

First, let's download the binary from the release page, and then move it to `/sbin` folder.
```bash
curl -LO https://github.com/envoyproxy/envoy/releases/download/v1.32.3/envoy-1.32.3-linux-x86_64
chmod +x envoy-1.32.3-linux-x86_64
mv envoy-1.32.3-linux-x86_64 /sbin/envoy
```

Setup and launch five Redis/Valkey instances in standalone mode. There is a helper tool to do this in OSX with Orbstack called [redisstack](https://github.com/riveraja/valkey-how-tos/tree/main/tools/redisstack).

Use the `redis-data-generator` to test sharding of hash sets.
```bash
curl -LO https://raw.githubusercontent.com/riveraja/valkey-how-tos/refs/heads/main/tools/dgen/redis-data-generator
chmod +x redis-data-generator
```

Create the systemd unit file in `/etc/systemd/system/envoy.service`
```
[Unit]
Description=Envoy

[Service]
ExecStart=/sbin/envoy -c /etc/envoy.yaml --log-path /var/log/envoy.log
Restart=always
RestartSec=5
KillMode=mixed
SyslogIdentifier=envoy
LimitNOFILE=640000

[Install]
WantedBy=multi-user.target
```

## Configuration

Here's a sample configuration.
```yaml
static_resources:
  listeners:
  - name: redis_listener
    address:
      socket_address:
        address: 0.0.0.0
        port_value: 6379
    filter_chains:
    - filters:
      - name: envoy.filters.network.redis_proxy
        typed_config:
          "@type": type.googleapis.com/envoy.extensions.filters.network.redis_proxy.v3.RedisProxy
          stat_prefix: egress_redis
          settings:
            op_timeout: 5s
            enable_hashtagging: true
          prefix_routes:
            catch_all_route:
              cluster: redis_cluster
  clusters:
  - name: redis_cluster
    type: STRICT_DNS  # static
    dns_lookup_family: V4_ONLY
    lb_policy: RING_HASH
    load_assignment:
      cluster_name: redis
      endpoints:
      - lb_endpoints:
        - endpoint:
            address:
              socket_address: { address: 198.19.249.29, port_value: 6379 }
        - endpoint:
            address:
              socket_address: { address: 198.19.249.104, port_value: 6379 }
        - endpoint:
            address:
              socket_address: { address: 198.19.249.155, port_value: 6379 }
        - endpoint:
            address:
              socket_address: { address: 198.19.249.208, port_value: 6379 }
        - endpoint:
            address:
              socket_address: { address: 198.19.249.48, port_value: 6379 }
admin:
  address:
    socket_address:
      address: 0.0.0.0
      port_value: 8001
```

Refer to the [redis_proxy](https://www.envoyproxy.io/docs/envoy/latest/api-v3/extensions/filters/network/redis_proxy/v3/redis_proxy.proto) extension's documentation for the parameters used in the configuration file sample.

## Generate data to test sharding

Use the `redis-data-generator` to insert hash sets.
```bash
./redis-data-generator -uri 198.19.249.36:6379 --size 60000 -batch 100 -type hset
2024-12-31 14:59:34 Inserting 60000 records
Elapsed time: 13.85
```

Checking the data distribution, we can see that data has been distributed across nodes almost equally.
```
[root@rs1 ~]# redis-cli dbsize
(integer) 11662
...
[root@rs2 ~]# redis-cli dbsize
(integer) 12526
...
[root@rs3 ~]# redis-cli dbsize
(integer) 12474
...
[root@rs4 ~]# redis-cli dbsize
(integer) 11587
...
[root@rs5 ~]# redis-cli dbsize
(integer) 11751
```
This is made possible by the `lb_policy` which was set to `RING_HASH`.

Let's do another test and this time we add a `-tag` to generate data.
```bash
./redis-data-generator -uri 198.19.249.36:6379 --size 6000 -batch 100 -type hset -tag avgo
2024-12-31 13:42:57 Inserting 6000 records
Elapsed time: 1.31
```

If we check again, we'll see that only one node was inserted with new data.
```
[root@rs1 ~]# redis-cli dbsize
(integer) 11662
...
[root@rs2 ~]# redis-cli dbsize
(integer) 18526
...
[root@rs3 ~]# redis-cli dbsize
(integer) 12474
...
[root@rs4 ~]# redis-cli dbsize
(integer) 11587
...
[root@rs5 ~]# redis-cli dbsize
(integer) 11751
```
This is because we enabled hashtagging in our `envoy.yaml` file.
```
[root@rs2 ~]# redis-cli scan 0
1) "5120"
2)  1) "emp:0000005944"
    2) "emp:0000041457"
    3) "emp:{avgo}:0000005730"
    4) "emp:0000010814"
    5) "emp:0000015790"
    6) "emp:{avgo}:0000003562"
    7) "emp:0000033762"
    8) "emp:{avgo}:0000001723"
    9) "emp:0000028987"
   10) "emp:{avgo}:0000001874"
```

With `enable_hashtagging: true`, envoy will compute the hash for the tag string ensuring that data for a specific tag will be stored in the same shard node.

