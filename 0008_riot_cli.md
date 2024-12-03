# Using RIOT with Valkey/Redis

RIOT or Redis Input/Output Tool is a multi-use tool. It can be used to generate data, replicate data from one Redis instance to another or from an SQL database to Redis or the other way around.

## Download RIOT and unzip the  file
```
curl -LO https://github.com/redis/riot/releases/download/v4.1.9/riot-standalone-4.1.9-linux-x86_64.zip
unzip riot-standalone-4.1.9-linux-x86_64.zip
```

## Generate random data
```
./riot-standalone-4.1.9-linux-x86_64/bin/riot generate —type STRING HASH LIST SET JSON —count 100000 —host=127.0.0.1 
```
Note that not all data types is supported by Valkey as of this writing.

## Data migration from one Redis instance to another
```
./riot-standalone-4.1.9-linux-x86_64/bin/riot --info replicate 198.19.249.56:6379 127.0.0.1:6379 
```

## Import data from a file/URL to Redis
```
./riot-standalone-4.1.9-linux-x86_64/bin/riot file-import http://storage.googleapis.com/jrx/beers.csv —header -h 127.0.0.1 -p 6379 hset —keyspace beer —key row
```
`--header` signifies that we're using the file's header as the fields
`hset` signifies that we're saving a hash set
`-keyspace beer` this will create a keyspace that starts with `beer:`
`-key row` this makes the `row` value as the key, eg `beer:12345`

