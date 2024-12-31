# Using RIOT with Valkey/Redis

**RIOT** or Redis Input/Output Tool is a multi-use tool. It can be used to generate data, replicate data from one Redis instance to another or from an SQL database to Redis or the other way around.

## Download RIOT and unzip the  file
```bash
curl -LO https://github.com/redis/riot/releases/download/v4.1.10/riot-standalone-4.1.10-linux-x86_64.zip
unzip riot-standalone-4.1.10-linux-x86_64.zip
```

## Generate random data
```bash
./riot-standalone-4.1.10-linux-x86_64/bin/riot generate --type STRING HASH LIST SET JSON --count 100000 --host=127.0.0.1 
```
Note that not all data types is supported by Valkey as of this writing.

## Data migration from one Redis instance to another
```bash
./riot-standalone-4.1.10-linux-x86_64/bin/riot --info replicate 198.19.249.56:6379 127.0.0.1:6379 
```

## Import data from a file/URL to Redis
```bash
./riot-standalone-4.1.10-linux-x86_64/bin/riot file-import http://storage.googleapis.com/jrx/beers.csv --header -h 127.0.0.1 -p 6379 hset —keyspace beer —key row
```
```bash
./riot-standalone-4.1.10-linux-x86_64/bin/riot file-import gs://jrx/beers.json --uri "redis://198.19.249.90:6379" json.set --keyspace beer --key id
```
`--header` signifies that we're using the file's header as the fields
`hset` signifies that we're saving a hash set
`-keyspace beer` this will create a keyspace that starts with `beer:`
`-key row` this makes the `row` value as the key, eg `beer:12345`

## Export data from Redis to a file
```bash
./riot-standalone-4.1.10-linux-x86_64/bin/riot file-export --uri "redis://198.19.249.90:6379" gen.json
```

## Import data from MySQL to Redis
```bash
./riot-standalone-4.1.10-linux-x86_64/bin/riot db-import --jdbc-url "jdbc:mysql://198.19.249.197:3306/encrypted_db" --jdbc-username riot --jdbc-password riot "SELECT * FROM sbtest LIMIT 1000" --uri "redis://198.19.249.90:6379/0" hset --keyspace sbtest --key id
```
```bash
./riot-standalone-4.1.10-linux-x86_64/bin/riot db-import --jdbc-url "jdbc:mysql://198.19.249.197:3306/encrypted_db" --jdbc-username riot --jdbc-password riot "SELECT * FROM sbtest LIMIT 1000" --uri "redis://198.19.249.90:6379/0" json.set --keyspace sbtest --key id
```

