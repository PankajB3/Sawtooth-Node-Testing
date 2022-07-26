# Steps To Setup a Sawtooth Node for Testing

## Using Docker for a Single Sawtooth Node

This procedure explains how to set up Hyperledger Sawtooth for application development using a multi-container Docker environment. It shows you how to start Sawtooth and connect to the necessary Docker containers, then walks you through the following tasks:
<ul>
<li>Checking the status of Sawtooth components
</li>
<li>Using Sawtooth commands to submit transactions, display block data, and view global state</li>
<li>Examining Sawtooth logs</li>
<li>Stopping Sawtooth and resetting the Docker environment</li>
</ul>

## About the Docker Test Node Environment
This Docker environment is a single Sawtooth node that is running a validator, a REST API, the Devmode consensus engine, and three transaction processors. The environment uses Devmode consensus and parallel transaction processing.

<strong>This application development environment requires Docker Engine and Docker Compose.
</strong>

In this procedure, you will open six terminal windows to connect to the Docker containers: one for each Sawtooth component and one to use for client commands.

### Step 1 : Add Sawtooth Docker Compose File
Find it <a href="https://github.com/hyperledger/sawtooth-core/blob/1-2/docker/compose/sawtooth-default.yaml">here</a>

### Step 2 :  Start the Sawtooth Docker Environment

Use the command, inside the directory which contains the <strong>sawtooth-default.yaml</strong> file.
```
user@host$ docker-compose -f sawtooth-default.yaml up
```
This command will also download the required images in the environment.
If this command gives error try following
```
docker-compose -f sawtooth-default.yaml up
```

### Step 3 : Log Into the Docker Client Container
Sawtooth includes commands that act as a client application. The client container is used to run these Sawtooth commands, which interact with the validator through the REST API.

```
docker exec -it sawtooth-shell-default bash
```
After executing the above command you will get a prompt like

<strong>root@{some number here}</strong>

In this procedure it is used for commands that should be run in the terminal window for the client container.

### Step 4: Confirm Connectivity to the REST API (for Docker)
1. To confirm that the REST API and validator are running and reachable from the client container, run this curl command:
```
curl http://rest-api:8008/blocks
```
2. To check connectivity from the host computer, open a new terminal window on your host system and run this curl command:
```
curl http://localhost:8008/blocks
```
If the validator and REST API are running and reachable, the output for each command should be similar to this example:
```
{
  "data": [
    {
      "batches": [],
      "header": {
        "batch_ids": [],
        "block_num": 0,
        "mconsensus": "R2VuZXNpcw==",
        "previous_block_id": "0000000000000000",
        "signer_public_key": "03061436bef428626d11c17782f9e9bd8bea55ce767eb7349f633d4bfea4dd4ae9",
        "state_root_hash": "708ca7fbb701799bb387f2e50deaca402e8502abe229f705693d2d4f350e1ad6"
      },
      "header_signature": "119f076815af8b2c024b59998e2fab29b6ae6edf3e28b19de91302bd13662e6e43784263626b72b1c1ac120a491142ca25393d55ac7b9f3c3bf15d1fdeefeb3b"
    }
  ],
  "head": "119f076815af8b2c024b59998e2fab29b6ae6edf3e28b19de91302bd13662e6e43784263626b72b1c1ac120a491142ca25393d55ac7b9f3c3bf15d1fdeefeb3b",
  "link": "http://rest-api:8008/blocks?head=119f076815af8b2c024b59998e2fab29b6ae6edf3e28b19de91302bd13662e6e43784263626b72b1c1ac120a491142ca25393d55ac7b9f3c3bf15d1fdeefeb3b",
  "paging": {
    "start_index": 0,
    "total_count": 1
  }
}
```

### Step 5: Use Sawtooth Commands as a Client
This step describes how to use the <b>intkey</b> and <b>sawtooth</b> commands to create and submit transactions, display blockchain and block data, and examine global state data.
We will be using client container for it.

#### (a) Creating and Submitting Transactions with intkey
The intkey command creates and submits IntegerKey transactions for testing purposes.

1. Use intkey create_batch to prepare batches of transactions that set a few keys to random values, then randomly increment and decrement those values. These batches are saved locally in the file batches.intkey.

Run following command in client cmd
```
intkey create_batch --count 10 --key-count 5
```

Output as
```
Writing to batches.intkey...
```

2. Use intkey load to submit the batches to the validator.
```
 intkey load -f batches.intkey --url http://rest-api:8008
 ```

 Output as
 ```
 batches: 11 batch/sec: 141.7800162868952
 ```

 #### (b) Submitting Transactions with sawtooth batch submit
In the example above, the intkey create_batch command created the file batches.intkey. Rather than using intkey load to submit these transactions, you could use sawtooth batch submit to submit them.

1. As before, create a batch of transactions:
```
intkey create_batch --count 10 --key-count 5
```
2. Submit the batch file with sawtooth batch submit:
```
sawtooth batch submit -f batches.intkey --url http://rest-api:8008
```

#### (c) Viewing Blockchain and Block Data with sawtooth block
The sawtooth block command displays information about the blocks stored on the blockchain.

1. Use sawtooth block list to display the list of blocks stored in state.
```
sawtooth block list --url http://rest-api:8008
```
2. From the output generated by sawtooth block list, copy the ID of a block you want to view, then paste it in place of {BLOCK_ID} in the following command:
```
sawtooth block show --url http://rest-api:8008 {BLOCK_ID}
```

#### (d) Viewing State Data with sawtooth state
The sawtooth state command lets you display state data. Sawtooth stores state data in a Merkle-Radix tree; for more information, see Global State.

1. Use sawtooth state list to list the nodes (addresses) in state:
```
sawtooth state list --url http://rest-api:8008
```
2. Use sawtooth state show to view state data at a specific address (a node in the Merkle-Radix database). Copy the address from the output of sawtooth state list, then paste it in place of {STATE_ADDRESS} in the following command:
```
sawtooth state show --url http://rest-api:8008 {STATE_ADDRESS}
```

### Step 6 : Stop the Sawtooth Docker Environment
1. Log out of the client container.

2. Enter CTRL-c from the window where you originally ran docker-compose
3. After all containers have shut down, run this docker-compose command:
```
docker-compose -f sawtooth-default.yaml down
```