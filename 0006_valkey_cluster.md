# Valkey Clustering Script

Redis/Valkey has a helper script to test clustering in a local environment. We will use this script to test how it works.

Let's first clone the Valkey repository:
```
git clone https://github.com/valkey-io/valkey
cd valkey
make -j 4
make test
```

After this step, you should have the valkey-server and other relevant executable binaries inside the `valkey/src` folder.

Go to the `./utils/create-cluster` folder to get to the `create-cluster` script.
```
cd utils/create-cluster/
```

Create the `config.sh` file in the same directory, then insert the settings section from the `create-cluster` script.
```
cat << EOF > ./config.sh
# Settings
BIN_PATH="$SCRIPT_DIR/../../src/"
CLUSTER_HOST=127.0.0.1
PORT=30000
TIMEOUT=2000
NODES=6
REPLICAS=1
PROTECTED_MODE=yes
ADDITIONAL_OPTIONS=""
EOF
```
In this setup we're creating a six-node cluster with 1 replica for each primary node.

Let's check the script usage by running the command without any arguments:
```
[root@valkey create-cluster]# ./create-cluster
Usage: ./create-cluster [start|create|stop|restart|watch|tail|tailall|clean|clean-logs|call]
start       -- Launch Valkey Cluster instances.
create [-f] -- Create a cluster using valkey-cli --cluster create.
stop        -- Stop Valkey Cluster instances.
restart     -- Restart Valkey Cluster instances.
watch       -- Show CLUSTER NODES output (first 30 lines) of first node.
tail <id>   -- Run tail -f of instance at base port + ID.
tailall     -- Run tail -f for all the log files at once.
clean       -- Remove all instances data, logs, configs.
clean-logs  -- Remove just instances logs.
call <cmd>  -- Call a command (up to 7 arguments) on all nodes.
```

## Start the cluster instances
To start the cluster instances use the `./create-cluster start` command.
```
[root@valkey create-cluster]# ./create-cluster start
Starting 30001
Starting 30002
Starting 30003
Starting 30004
Starting 30005
Starting 30006
```

## Create the cluster
Let's create a new cluster with the `./create-cluster create -f` command.
```
[root@valkey create-cluster]# ./create-cluster create -f
>>> Performing hash slots allocation on 6 node(s)...
Primary[0] -> Slots 0 - 5460
Primary[1] -> Slots 5461 - 10922
Primary[2] -> Slots 10923 - 16383
Adding replica 127.0.0.1:30005 to 127.0.0.1:30001
Adding replica 127.0.0.1:30006 to 127.0.0.1:30002
Adding replica 127.0.0.1:30004 to 127.0.0.1:30003
>>> Trying to optimize replicas allocation for anti-affinity
[WARNING] Some replicas are in the same host as their primary
M: 3dd001f45c566ad80118d877a9b4e9a89202b92e 127.0.0.1:30001
   slots:[0-5460] (5461 slots) master
M: 0b075ad717dd9af2cb78599694ced2a15776f04c 127.0.0.1:30002
   slots:[5461-10922] (5462 slots) master
M: 505d42693b405af4d6640c85ab57503e7aad91c3 127.0.0.1:30003
   slots:[10923-16383] (5461 slots) master
S: 6c5ba9b884d00f439753ba578a9693e87b49ab92 127.0.0.1:30004
   replicates 0b075ad717dd9af2cb78599694ced2a15776f04c
S: 1c3c674f5d7f742bb79705a7277ac0ff09ac5dea 127.0.0.1:30005
   replicates 505d42693b405af4d6640c85ab57503e7aad91c3
S: 9ce7b70d58cb54e03b6239c8ab56529e1a93d312 127.0.0.1:30006
   replicates 3dd001f45c566ad80118d877a9b4e9a89202b92e
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join
.
>>> Performing Cluster Check (using node 127.0.0.1:30001)
M: 3dd001f45c566ad80118d877a9b4e9a89202b92e 127.0.0.1:30001
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
S: 6c5ba9b884d00f439753ba578a9693e87b49ab92 127.0.0.1:30004
   slots: (0 slots) slave
   replicates 0b075ad717dd9af2cb78599694ced2a15776f04c
S: 1c3c674f5d7f742bb79705a7277ac0ff09ac5dea 127.0.0.1:30005
   slots: (0 slots) slave
   replicates 505d42693b405af4d6640c85ab57503e7aad91c3
M: 505d42693b405af4d6640c85ab57503e7aad91c3 127.0.0.1:30003
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
M: 0b075ad717dd9af2cb78599694ced2a15776f04c 127.0.0.1:30002
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
S: 9ce7b70d58cb54e03b6239c8ab56529e1a93d312 127.0.0.1:30006
   slots: (0 slots) slave
   replicates 3dd001f45c566ad80118d877a9b4e9a89202b92e
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
```
We have three primary nodes with one replica each. Each primary node is assigned a range of slots equally divided among all three primary instances.
```
Primary[0] -> Slots 0 - 5460
Primary[1] -> Slots 5461 - 10922
Primary[2] -> Slots 10923 - 16383
```

## Watch the cluster
The `./create-cluster watch` command basically polls the current status of the cluster every second:
```Mon Nov 25 15:58:36 PST 2024
6c5ba9b884d00f439753ba578a9693e87b49ab92 127.0.0.1:30004@40004 slave 0b075ad717dd9af2cb78599694ced2a15776f04c 0 1732521516206 2 connected
1c3c674f5d7f742bb79705a7277ac0ff09ac5dea 127.0.0.1:30005@40005 slave 505d42693b405af4d6640c85ab57503e7aad91c3 0 1732521516313 3 connected
505d42693b405af4d6640c85ab57503e7aad91c3 127.0.0.1:30003@40003 master - 0 1732521516313 3 connected 10923-16383
0b075ad717dd9af2cb78599694ced2a15776f04c 127.0.0.1:30002@40002 master - 0 1732521516000 2 connected 5461-10922
3dd001f45c566ad80118d877a9b4e9a89202b92e 127.0.0.1:30001@40001 myself,master - 0 0 1 connected 0-5460
9ce7b70d58cb54e03b6239c8ab56529e1a93d312 127.0.0.1:30006@40006 slave 3dd001f45c566ad80118d877a9b4e9a89202b92e 0 1732521516313 1 connected
```

## Stop and cleanup
To remove the testing instances, use the `stop` and `clean` commands.
```
[root@valkey create-cluster]# ./create-cluster stop
Stopping 30001
Stopping 30002
Stopping 30003
Stopping 30004
Stopping 30005
Stopping 30006
[root@valkey create-cluster]# ./create-cluster clean
Cleaning *.log
Cleaning appendonlydir-*
Cleaning dump-*.rdb
Cleaning nodes-*.conf
```

## Adding new features to the script
Here we're adding a feature to start new nodes:
```
if [ "$1" == "startnode" ]
then
    PORT=$2
    echo "Starting $PORT"
    $BIN_PATH/valkey-server --port $PORT --protected-mode $PROTECTED_MODE --cluster-enabled yes --cluster-config-file nodes-${PORT}.conf --cluster-node-timeout $TIMEOUT --appendonly yes --appendfilename appendonly-${PORT}.aof --appenddirname appendonlydir-${PORT} --dbfilename dump-${PORT}.rdb --logfile ${PORT}.log --daemonize yes --enable-protected-configs yes --enable-debug-command yes --enable-module-command yes ${ADDITIONAL_OPTIONS}
    exit 0
fi
```
To start the new nodes:
```
./create-cluster startnode 30007
./create-cluster startnode 30008
```
This will start two new standalone cluster nodes.

We can add the new nodes either as a primary or as a replica to an existing primary.
```
if [ "$1" == "addnode" ]
then
    PORT1=$2
    PORT2=$3
    OPTS=$4
    echo "Adding node on port $PORT1"
    if [ -z "${OPTS}" ]
    then
        $BIN_PATH/valkey-cli --cluster add-node ${CLUSTER_HOST}:${PORT1} ${CLUSTER_HOST}:${PORT2}
    else
        $BIN_PATH/valkey-cli --cluster add-node ${CLUSTER_HOST}:${PORT1} ${CLUSTER_HOST}:${PORT2} --cluster-slave --cluster-master-id ${OPTS}
    fi
    exit 0
fi
```

To use this, run the following command:
```
./create-cluster <NEW_PORT_NUMBER> <ANY_EXISTING_PORT> <OPTIONAL:PRIMARY_CLUSTER_ID>
```
To create a primary instance:
```
./create-cluster 30007 30001
```
To create a replica of an existing primary:
```
./create-cluster 30008 30001 <cluster_id_of_node30007>
```

If an additional primary is added to the cluster we need to rebalance the shard slots accordingly. We can add this snippet to the script for that purpose:
```
if [ "$1" == "reshard" ]
then
    PORT=$2
    echo "Resharding the cluster"
    $BIN_PATH/valkey-cli -p $PORT --cluster reshard $CLUSTER_HOST:${PORT}
    exit 0
fi
```
To use this, run this command:
```
./create-cluster reshard 30007
```
Then follow the instructions on resharding. Since there are 16383 slots and there are 4 shards we use the value `4096` when prompted.

