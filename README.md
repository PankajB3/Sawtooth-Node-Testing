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