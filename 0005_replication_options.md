# Valkey Replication Parameters

Important parameters needed when configuring replication in Valkey.

### replicaof

`replicaof [master-host] [master-port]` - sets of the server as a replica of a master, triggering a partial or a full sync with the master.

### replica-serve-stale-data

`replica-server-stale-data [string]` - if 'yes', a replica that loses connection from its master can continue to serve stale data to clients, if 'no' the replica responds with an error for all client data requests.

### client-output-buffer-limit

`client-output-buffer-limit slave [hard-limit] [soft-limit] [seconds]` - this configuration helps prevent memory exhaustion and ensures that Valkey can manage its resources effectively when dealing with multiple replicas that might be experience network issues.

`hard-limit` - the maximum size of the output buffer. If a replica exceeds this limit, Valkey will close the connection.

`soft-limit` - the maximum sze there Valkey will start to apply back pressure. If the buffer exceeds this size, Valkey will start to notify the replicas on possible performance issues.

`time-in-seconds` - measured in seconds. If the `soft-limit` is sustained for the whole duration of the `time-in-seconds` the replica will be disconnected.

### repl-backlog-size

`repl-backlog-size [number]` - is the replication backlog size. If and when a replica reconnects to a master if the repl-backlog-size is reached the master will initiate a full synchronization otherwise it will be a partial sync.

Before a replica is put into maintenance mode, it would be good to adjust the value of repl-backlog-size so as to ensure that a partial sync will happen instead of a full sync.

### repl-diskless-sync

`repl-diskless-sync [string]` - whether or not the replication will be disk-based or diskless. Diskless replication avoids writing data to disk when syncing replicas. Downside to this is that more memory will be used in the master.
