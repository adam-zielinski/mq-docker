# IBM MQ for usage with NiFi

[Build](#build)

[Run](#run)

[MQ explorer & connection](#mq-connection)

## Build
This will create an image `cstanciu/nifi-mq`

```
make build
```

## Run
This will run the image `cstanciu/nifi-mq` as a container

```
make run
```

## MQ Connection 
MQ explorer (eclipse) it's used as the IBM MQ manager, to visualy configure the server and to test it
To install it, just take the latest Eclipse + [MQ plugin](https://marketplace.eclipse.org/content/ibm-mq-explorer-version-9)


### MQ Explorer Eclipse plug-in
From the Marketplace, take the URL mentioned "Add this URL to your Eclipse Installation to reach this solution's update site."
`http://public.dhe.ibm.com/software/integration/wmq/explorer/v9/updates/` and paste it in the Install new software window => `Help -> Install new software -> Url: ....`

### Eclipse MQ perspective
Switch perspective
```
Window -> Perspective -> Open perspective -> MQ Explorer
```

### Connection
1. Under `IBM MQ`
2. Right-click `Queue Managers` => `Add Remote Queue Manager`
3. Windows:
- Select the queue manager ...
Queue manager name = QM1 (from the run command: docker run --env MQ_QMGR_NAME=QM1 ....)
Connect directly = enabled
-> Next

- Specify new connection details
Host name = localhost (or `docker-machine ip`, if you are running through kitematic)
Server-connection channel = PASSWORD.SVRCONN (from [config.mqsc](config.mqsc))
-> Next 

- Specify security exist details
-> Next

- Specify user identification details
Enable user identification = enabled
Userid = <mac user/ nifi user> (should exist [in linux namespace](Dockerfile#L2) )
Prompt for password = enabled
-> Finish

### JMS configuration
For each queue, you can create a "linked" JMS queue.

#### Create JMS context
You need first a JMS context.
From the explorer under IBM MQ
- JMS Administered objects -> Add initial context
File system = enabled
Bindings directory = </home/user/jndi-dir> (this could be linked with the jms producer/consumer)

#### Create JMS queue
Right-click on the queue -> Create JMS queue.
Select the JMS context


## NiFi

To connect to IBM MQ from NiFi, you need the jms jars from IBM MQ (as ibm is the implementor), and to configure the different services and processors in Nifi.

### JMS jar
The JMS jar must match the IBM MQ server which you are targeting, so it's easier to take it from the server installation (also because it's a pain to only find them under the IBM MQ Patches site)

The jar is `/opt/mqm/java/lib/*mqjms.jar`

The jar can be copied to `/shared-data`, which is linked to a [host dir](Makefile#L9), or use [docker cp](https://docs.docker.com/engine/reference/commandline/cp/)

### Services and processors

Controller Services:
- JMSConnectionFactoryProviderDefinition
- JMSConnectionFactoryProvider

Processors: 
- ConsumeJMS
- PublishJMS

