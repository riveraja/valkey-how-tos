# How Redis behaves when it reaches the maxmemory threshold

As we all know Redis/Valkey is an in-memory data store, since memory is a finite resource in a server it is recommended to configure Redis/Valkey with the `maxmemory` directive. If memory usage reaches 80% this indicates that the instance is under memory pressure. If not managed on time, you risk crashing the instance due to out of memory. The recommended `maxmemory` setting is at 80% and keep 20% of free memory available.

## What is maxmemory-policy in Redis/Valkey

The `maxmemory-policy` will determine how Redis/Valkey behaves if and when the maxmemory limit is reached. The following policies are available:

- `noeviction`: New values aren’t saved when memory limit is reached. When a database uses replication, this applies to the primary database
- `allkeys-lru`: Keeps most recently used keys; removes least recently used (LRU) keys
- `allkeys-lfu`: Keeps frequently used keys; removes least frequently used (LFU) keys
- `volatile-lru`: Removes least recently used keys with a time-to-live (TTL) set.
- `volatile-lfu`: Removes least frequently used keys with a TTL set.
- `allkeys-random`: Randomly removes keys to make space for the new data added.
- `volatile-random`: Randomly removes keys with a TTL set.
- `volatile-ttl`: Removes keys with a TTL set, the keys with the shortest remaining time-to-live value first.

The default `maxmemory-policy` is `noeviction`.

For `allkeys-*` policies there is no need to set a TTL on the key for it to be expired. For all other policies will require a TTL to be set.

Other references:

- [How to Choose Eviction Policies on Caching Database Clusters](https://docs.digitalocean.com/products/databases/redis/how-to/choose-eviction-policies/)
- [Key Eviction - Valkey docs](https://valkey.io/topics/lru-cache/)
- [Key Eviction - Redis docs](https://redis.io/docs/latest/develop/reference/eviction/)
- [Is maxmemory the Maximum Value of Used memory?](https://redis.io/kb/doc/1jbxid5qq7/is-maxmemory-the-maximum-value-of-used-memory)
