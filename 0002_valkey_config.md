# How to set and update Redis/Valkey configuration

In Redis/Valkey, the `valkey-server` is started pointing to a configuration file:
```
codespace ➜ /workspace $ /usr/bin/valkey-server /workspace/conf/valkey.conf 
20426:C 04 Sep 2024 06:31:47.093 * oO0OoO0OoO0Oo Valkey is starting oO0OoO0OoO0Oo
20426:C 04 Sep 2024 06:31:47.094 * Valkey version=7.2.5, bits=64, commit=00000000, modified=0, pid=20426, just started
20426:C 04 Sep 2024 06:31:47.094 * Configuration loaded
20426:M 04 Sep 2024 06:31:47.097 * monotonic clock: POSIX clock_gettime
20426:M 04 Sep 2024 06:31:47.104 # Failed to write PID file: Permission denied
                .+^+.                                                
            .+#########+.                                            
        .+########+########+.           Valkey 7.2.5 (00000000/0) 64 bit
    .+########+'     '+########+.                                    
 .########+'     .+.     '+########.    Running in standalone mode
 |####+'     .+#######+.     '+####|    Port: 6379
 |###|   .+###############+.   |###|    PID: 20426                     
 |###|   |#####*'' ''*#####|   |###|                                 
 |###|   |####'  .-.  '####|   |###|                                 
 |###|   |###(  (@@@)  )###|   |###|          https://valkey.io      
 |###|   |####.  '-'  .####|   |###|                                 
 |###|   |#####*.   .*#####|   |###|                                 
 |###|   '+#####|   |#####+'   |###|                                 
 |####+.     +##|   |#+'     .+####|                                 
 '#######+   |##|        .+########'                                 
    '+###|   |##|    .+########+'                                    
        '|   |####+########+'                                        
             +#########+'                                            
                '+v+'                                                

20426:M 04 Sep 2024 06:31:47.112 * Server initialized
20426:M 04 Sep 2024 06:31:47.114 * Loading RDB produced by valkey version 7.2.5
20426:M 04 Sep 2024 06:31:47.114 * RDB age 41 seconds
20426:M 04 Sep 2024 06:31:47.114 * RDB memory usage when created 0.94 Mb
20426:M 04 Sep 2024 06:31:47.114 * Done loading RDB, keys loaded: 0, keys expired: 0.
20426:M 04 Sep 2024 06:31:47.115 * DB loaded from disk: 0.002 seconds
20426:M 04 Sep 2024 06:31:47.115 * Ready to accept connections tcp
```

The configuration file will list down all settings required by Redis/Valkey to start up.

## Preview the runtime configuration

To check the runtime configuration in Redis/Valkey, we can use the `CONFIG GET option` command. Where `option` can be the config option such as `port` or a wildcard name for example `bind*` or `*` to get all options available.
```
127.0.0.1:6379> config get port
1) "port"
2) "6379"
127.0.0.1:6379> config get bind*
1) "bind-source-addr"
2) ""
3) "bind"
4) "0.0.0.0 -::1"
127.0.0.1:6379> config get *
  1) "tls-protocols"
  2) ""
  3) "daemonize"
  4) "no"
  5) "enable-module-command"
  6) "no"
  7) "dir"
  8) "/workspace/data"
  9) "rdbcompression"
 10) "yes"
<REDACTED FOR BREVITY>
```

## Change the runtime configuration

To update/change the runtime config using the `CONFIG SET option` command. This command will not take a wildcard value.
```
127.0.0.1:6379> config set bind "127.0.0.1 -::1"
OK
127.0.0.1:6379> config get bind
1) "bind"
2) "127.0.0.1 -::1"
```

## Persist the changes to the runtime configuration

This change will not persist to the configuration file, therefore we should persist it using `CONFIG REWRITE` command.
```
127.0.0.1:6379> config rewrite
OK
```
