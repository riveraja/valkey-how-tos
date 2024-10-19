# Valkey Replication Basics

This article will give a quick rundown on how to setup replication between two Valkey instances. Technically, we want each Valkey instance to be hosted in separate host servers. However, in this case, we are setting everything up in the same host for a simple demonstration.

## The configuration file

Basically, the only configurations needed to setup replication between two Valkey instances are the following:
```
# replicaof [master_host] [master_port]
replicaof 127.0.0.1 7001
```

If the master is configured with `reuirepass` then we use `masterauth` on the replica to authenticate during sync.

In this demo, I will be using this configuration for both instances with slight changes.
```
bind 127.0.0.1
protected-mode no
port 7001

daemonize yes
pidfile /var/run/redis_7001.pid
loglevel notice
logfile "/home/jerichorivera/rc1/redis.log"

databases 8
always-show-logo no
save 3600 1

rdbcompression yes
rdbchecksum yes
dir /home/jerichorivera/rc1/
dbfilename dump.rdb

requirepass adminredis
masterauth adminredis

appendonly yes
appendfilename "redis.aof"
appenddirname "aof"
```

Only changes on the replica server would be the `port`, `pidfile`, `logfile`, and `dir`.

## Start the master

To start the master server you just execute:
```
# valkey-server [path/to/config-file]
valkey-server rc1/redis.conf
```

## Start the replica

To start the replica server execute:
```
# valkey-server [path/to/config-file]
valkey-server rc2/redis.conf
```

## Set replication

Let's start replication on the replica server:
```
# replicaof [master-host] [master-port]
replicaof 127.0.0.1 7002
```

## How replication is initialized

When the replica sends a request to the master for synchronization, it will first determine if a partial sync can happen, if not then a full sync will be performed.

During a full sync:
- a child process is forked from the parent process
- a BGSAVE snapshot is executed that uses copy-on-write method
- a pipe is created to stream data to the replica sockets
- data is stream through the pipe to the replicas
- writes to the primary will be kept in the replication buffer

During a partial sync:
- replica sends its replication ID and offsets they processed
- master compares the replication ID and offset to the replication backlog
- if the offset is within the replication backlog a partial sync will transpire

## Types of replication

Disk-based replication:
- set repl-diskless-sync=no
- a forked child process is started and starts a background saving process to produce an RDB file
- master starts to buffer all new writes
- the child process streams the produced RBD file to the replica
- the replica saves it on disk and then loads it into memory
- the replica then sends all buffered writes to the replica

Disk-less replication:
- set repl-diskless-sync=yes
- the default since 7.x
- this method uses replica sockets to stream the snapshot from the master to the replica
- no disk file is used during the sync process

