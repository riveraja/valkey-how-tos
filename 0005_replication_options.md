# Valkey Replication Parameters

Important parameters needed when configuring replication in Valkey.

### client-output-buffer-limit

`client-output-buffer-limit slave [hard-limit] [soft-limit] [time-in-seconds]`
- this configuration helps prevent memory exhaustion and ensures that Valkey can manage its resources effectively when dealing with multiple replicas that might be experience network issues.

`hard-limit` - the maximum size of the output buffer. If a replica exceeds this limit, Valkey will close the connection.

`soft-limit` - the maximum sze there Valkey will start to apply back pressure. If the buffer exceeds this size, Valkey will start to notify the replicas on possible performance issues.

`time-in-seconds` - measured in seconds. If the `soft-limit` is sustained for the whole duration of the `time-in-seconds` the replica will be disconnected.

### repl-backlog-size

`repl-backlog-size` - is the replication backlog size. If the backlog size is reached