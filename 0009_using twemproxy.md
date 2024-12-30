# Using Twemproxy with Redis/Valkey

Twemproxy, aka nutcracker, is a fast and lightweight load balancer proxy for memcached and redis protocol. This proxy was created by Twitter which was open sourced way back in February 2012. It was primarily developed to reduce open connections to their cache servers. It has evolved to support more redis commands, data sharding, tag sharding, redis health checks and eviction.

## Use cases

Twemproxy is useful for the following use cases

- consistent hashed sharding using ketama algorithm to distribute data to multiple backend nodes that are completely unaware of each other.
- load balance reads to multiple backend nodes using a specific distribution algorithm
- send writes to selected write nodes only

## Build from source

To install twemproxy, we build it from source.

```bash
$ git clone https://github.com/twitter/twemproxy
$ cd twemproxy/
$ autoreconf -fvi
$ ./configure --enable-debug=full
$ make && sudo make install
```

## Configuration parameters

The configuration file uses a YAML format specified by a `-c` or `--conf-file` in the command line.

- **listen** - the listening address and port or the absolute path of the socket file eg 127.0.0.1:6379 or /var/run/proxy.sock
- **client_connections** - the maximum number of connections allowed from redis clients
- **hash** - the name of the hash function, accepted values (one_at_a_time, md5, crc16, crc32, crc32a, fnv1_64, fnv1a_64, fnv1_32, fnv1a_32, hsieh, murmur, jenkins)
- **hash_tag** - a two-character string that specifies the part of the key used for hashing
- **distribution** - the key distribution mode (ketama, modula, random)
- **timeout** - value in milliseconds, twemproxy waits for to establish a connection to the server or receive a response from a server
- **backlog** - the TCP backlog
- **tcpkeepalive** - a boolean value that controls if tcp keepalive is enabled for connections to servers
- **preconnect** - a boolean value that controls if twemproxy should preconnect to all servers in the pool on process start
- **redis** - a boolean value that controls if a server pool peaks redis or memcached protocol
- **redis_auth** - authenticate to the redis server on connect
- **redis_db** - the DB number to use on the pool servers
- **server_connections** - the maximum number of connections that can be opened to each server
- **auto_reject_hosts** - a boolean value that controls if a server should be ejected temporarily when it fails consecutively server_failure_limit times.
- **server_retry_timeout** - timeout value in milliseconds to wait for before retrying on a temporary ejected server, when auto_eject_hosts is true
- **server_failure_limit** - the number of consecutive failures on a server that would lead to it being temporarily ejected when auto_eject_hosts is true
- **servers** - a flat list of server address:port:weight for the server pool

Minimal configuration for a setup with five backend nodes:
```yaml
alpha:
  listen: 0.0.0.0:22121
  hash: fnv1a_64
  hash_tag: "{}"
  timeout: 400
  distribution: ketama
  auto_eject_hosts: true
  redis: true
  server_retry_timeout: 2000
  server_failure_limit: 1
  servers:
   - 198.19.249.232:6379:1 rs1
   - 198.19.249.41:6379:1 rs2
   - 198.19.249.82:6379:1 rs3
   - 198.19.249.100:6379:1 rs4
   - 198.19.249.91:6379:1 rs5
```

To check if the configuration file is in the right format and syntax, use the `-t` or `--test-conf` option like this:
```bash
$ nutcracker -c /etc/proxy.cnf -t
$ nutcracker: configuration file '/etc/proxy.cnf' syntax is ok
```

## Starting the proxy

To start the proxy, run the command:
```bash
$ nutcracker -c /etc/proxy.cnf -d -o /var/log/proxy.log -p /var/run/proxy.pid
```

Or start with verbose logging:
```bash
$ nutcracker -c /etc/proxy.cnf -d -o /var/log/proxy.log -p /var/run/proxy.pid -v 9
```

## Run tests with redis-data-generator

Use the `redis-data-generator` tool to test.

Simple `hset` command to insert key `emp:0000000001`
```bash
$ curl -LO https://raw.githubusercontent.com/riveraja/valkey-how-tos/refs/heads/main/tools/dgen/redis-data-generator
$ dnf install perl-Redis perl-Time-HiRes perl-Data-Faker -y
$ chmod +x redis-data-generator
$ ./redis-data-generator -size 100000 -batch 100 -type hset -uri 127.0.0.1:22121
```

Simple `hset` command that insert keys with tags `emp:{tag_name}:0000000001`
```bash
$ ./redis-data-generator -size 100000 -batch 100 -type hset -tag some_tag -uri 127.0.0.1:22121
```

## Observations

Observe that for the first command using the data generator script, the data was distributed across all nodes, while in the second set, all the data generated with a tag was sent to a single node only.
