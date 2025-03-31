# Setting up HAProxy for Valkey Cluster in Docker

Let's setup a simple docker environment as a reference architecture for a nine node Valkey cluster (3 masters with 2 replicas each) with a HAProxy service in the frontend.

## Prerequisites

This setup requires Docker engine installed in your system.

### Pre-download the docker images for use

```bash
docker pull haproxy:2.9.15-alpine3.21
docker pull valkey/valkey:8.1-alpine3.21
```

### Prepare the folders

```bash
mkdir -p docker/haproxy docker/valkey
```

### Download the configuration files

```bash
cd docker/
curl -LO https://github.com/riveraja/valkey-how-tos/raw/refs/heads/main/tools/docker/haproxy/haproxy.cfg
curl -LO https://github.com/riveraja/valkey-how-tos/raw/refs/heads/main/tools/docker/valkey/valkey.conf
mv haproxy.cfg haproxy/
mv valkey.conf valkey/
```
### HAProxy config file

```
listen 6379-valkey-cluster
    bind 0.0.0.0:6379
    mode tcp
    balance leastconn
    timeout client 30s
    timeout server 30s
    timeout connect 10s

    option redis-check
    
    server valkey1 valkey1:6379 check inter 1s observe layer4 error-limit 3 on-error mark-down on-marked-down shutdown-sessions
    server valkey2 valkey2:6379 check inter 1s observe layer4 error-limit 3 on-error mark-down on-marked-down shutdown-sessions
    server valkey3 valkey3:6379 check inter 1s observe layer4 error-limit 3 on-error mark-down on-marked-down shutdown-sessions
    server valkey4 valkey4:6379 check inter 1s observe layer4 error-limit 3 on-error mark-down on-marked-down shutdown-sessions
    server valkey5 valkey5:6379 check inter 1s observe layer4 error-limit 3 on-error mark-down on-marked-down shutdown-sessions
    server valkey6 valkey6:6379 check inter 1s observe layer4 error-limit 3 on-error mark-down on-marked-down shutdown-sessions
    server valkey7 valkey7:6379 check inter 1s observe layer4 error-limit 3 on-error mark-down on-marked-down shutdown-sessions
    server valkey8 valkey8:6379 check inter 1s observe layer4 error-limit 3 on-error mark-down on-marked-down shutdown-sessions
    server valkey9 valkey9:6379 check inter 1s observe layer4 error-limit 3 on-error mark-down on-marked-down shutdown-sessions

frontend stats
    mode http
    bind 0.0.0.0:8080
    stats enable
    stats uri /stats
    stats refresh 10s
    stats admin if TRUE
    stats auth admin:admin

```

### Download the compose.yml file

```bash
curl -LO https://github.com/riveraja/valkey-how-tos/raw/refs/heads/main/tools/docker/compose.yaml
```

### Launch the containers

```bash
docker compose up -d

[+] Running 11/11
 ✔ Network docker_valkey-network  Created                                                                                                                                          0.0s 
 ✔ Container docker-valkey9-1     Started                                                                                                                                          0.5s 
 ✔ Container docker-haproxy-1     Started                                                                                                                                          0.4s 
 ✔ Container docker-valkey8-1     Started                                                                                                                                          0.5s 
 ✔ Container docker-valkey2-1     Started                                                                                                                                          0.6s 
 ✔ Container docker-valkey3-1     Started                                                                                                                                          0.6s 
 ✔ Container docker-valkey7-1     Started                                                                                                                                          0.5s 
 ✔ Container docker-valkey5-1     Started                                                                                                                                          0.5s 
 ✔ Container docker-valkey1-1     Started                                                                                                                                          0.5s 
 ✔ Container docker-valkey6-1     Started                                                                                                                                          0.4s 
 ✔ Container docker-valkey4-1     Started                                                                                                                                          0.4s 
```

 ### Access one of the containers and create the cluster

 ```bash
 docker exec -it docker-valkey1-1 sh
/data # valkey-cli --cluster create valkey1:6379 valkey2:6379 valkey3:6379 valkey4:6379 valkey5:6379 valkey6:6379 valkey7:6379 valkey8:6379 valkey9:6379 --cluster-replicas 2
>>> Performing hash slots allocation on 9 node(s)...
Primary[0] -> Slots 0 - 5460
Primary[1] -> Slots 5461 - 10922
Primary[2] -> Slots 10923 - 16383
Adding replica valkey5:6379 to valkey1:6379
Adding replica valkey6:6379 to valkey1:6379
Adding replica valkey7:6379 to valkey2:6379
Adding replica valkey8:6379 to valkey2:6379
Adding replica valkey9:6379 to valkey3:6379
Adding replica valkey4:6379 to valkey3:6379
M: 7bac966a8f387b569dd1eba6a88607a8f15a7776 valkey1:6379
   slots:[0-5460] (5461 slots) master
M: 30fa287337f6de9028109a16e7c7929f12b532fe valkey2:6379
   slots:[5461-10922] (5462 slots) master
M: 8afa9c2d3360aec4bd1a52f16f49311b089f3cc2 valkey3:6379
   slots:[10923-16383] (5461 slots) master
S: 1575f9e2c5e63aa6a7694f8467a83b6772cc406e valkey4:6379
   replicates 8afa9c2d3360aec4bd1a52f16f49311b089f3cc2
S: 15ffb81cd3bb540a40c40e3301ccbcc5ecb2e30a valkey5:6379
   replicates 7bac966a8f387b569dd1eba6a88607a8f15a7776
S: 5abe6372b1e1d73079d4290dfede0a41c6793213 valkey6:6379
   replicates 7bac966a8f387b569dd1eba6a88607a8f15a7776
S: 341f8bc6d0b3bb8cacf9488869c6956b12b26270 valkey7:6379
   replicates 30fa287337f6de9028109a16e7c7929f12b532fe
S: 508e784008c5e510e1e980855b840270dba1d2c9 valkey8:6379
   replicates 30fa287337f6de9028109a16e7c7929f12b532fe
S: 1f3ebd5c598466dee7b045d6e00187e39600aad3 valkey9:6379
   replicates 8afa9c2d3360aec4bd1a52f16f49311b089f3cc2
Can I set the above configuration? (type 'yes' to accept): yes
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join
.
>>> Performing Cluster Check (using node valkey1:6379)
M: 7bac966a8f387b569dd1eba6a88607a8f15a7776 valkey1:6379
   slots:[0-5460] (5461 slots) master
   2 additional replica(s)
S: 5abe6372b1e1d73079d4290dfede0a41c6793213 192.168.97.4:6379
   slots: (0 slots) slave
   replicates 7bac966a8f387b569dd1eba6a88607a8f15a7776
M: 30fa287337f6de9028109a16e7c7929f12b532fe 192.168.97.10:6379
   slots:[5461-10922] (5462 slots) master
   2 additional replica(s)
S: 1f3ebd5c598466dee7b045d6e00187e39600aad3 192.168.97.7:6379
   slots: (0 slots) slave
   replicates 8afa9c2d3360aec4bd1a52f16f49311b089f3cc2
S: 508e784008c5e510e1e980855b840270dba1d2c9 192.168.97.8:6379
   slots: (0 slots) slave
   replicates 30fa287337f6de9028109a16e7c7929f12b532fe
M: 8afa9c2d3360aec4bd1a52f16f49311b089f3cc2 192.168.97.11:6379
   slots:[10923-16383] (5461 slots) master
   2 additional replica(s)
S: 15ffb81cd3bb540a40c40e3301ccbcc5ecb2e30a 192.168.97.3:6379
   slots: (0 slots) slave
   replicates 7bac966a8f387b569dd1eba6a88607a8f15a7776
S: 1575f9e2c5e63aa6a7694f8467a83b6772cc406e 192.168.97.5:6379
   slots: (0 slots) slave
   replicates 8afa9c2d3360aec4bd1a52f16f49311b089f3cc2
S: 341f8bc6d0b3bb8cacf9488869c6956b12b26270 192.168.97.6:6379
   slots: (0 slots) slave
   replicates 30fa287337f6de9028109a16e7c7929f12b532fe
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
/data # exit

What's next:
    Try Docker Debug for seamless, persistent debugging tools in any container or image → docker debug docker-valkey1-1
    Learn more at https://docs.docker.com/go/debug-cli/
```

### Test the connections through HAProxy

```bash
docker exec -it docker-valkey1-1 sh
/data # redis-cli -c -h haproxy get hello
"world"
/data # redis-cli -c -h haproxy get foo
"bar"
```

### HAProxy dashboard

Visit `http://localhost:8080/stats` to check the dashboard.

### Destroy the containers

```bash
docker compose down

[+] Running 11/11
 ✔ Container docker-valkey1-1     Removed                                                                                                                                          0.3s 
 ✔ Container docker-valkey6-1     Removed                                                                                                                                          0.6s 
 ✔ Container docker-valkey9-1     Removed                                                                                                                                          0.4s 
 ✔ Container docker-haproxy-1     Removed                                                                                                                                         10.2s 
 ✔ Container docker-valkey3-1     Removed                                                                                                                                          0.5s 
 ✔ Container docker-valkey8-1     Removed                                                                                                                                          0.6s 
 ✔ Container docker-valkey7-1     Removed                                                                                                                                          0.5s 
 ✔ Container docker-valkey4-1     Removed                                                                                                                                          0.4s 
 ✔ Container docker-valkey2-1     Removed                                                                                                                                          0.5s 
 ✔ Container docker-valkey5-1     Removed                                                                                                                                          0.7s 
 ✔ Network docker_valkey-network  Removed                                                                                                                                          0.1s 
```