# Operations

## Debug

If you want to have more detailed output set the `DEBUG` env variable to non-zero:

```
DEBUG=1 cbd some_command
```

## Troubleshoot

You can use the `doctor` command to diagnose your environment.
It can reveal some common problems with your docker or boot2docker configuration and it also checks the cbd versions.

```
cbd doctor
```

## Logs

The aggregated logs of all the Cloudbreak components can be checked with:

```
cbd logs
```

It can also be used to check the logs of an individual docker container. To see only the logs of the Cloudbreak backend:

```
cbd logs cloudbreak
```

You can also check the individual logs of `uluwatu`, `periscope`, and `identity`.

## Cloudbreak Deployer Update

The cloudbreak-deployer tool is capable of upgrading itself to a newer version.

Please apply the following steps on the console:
- Update command shall be executed as **root**. In order to get root privileges execute:
```
   sudo -i
```
- Update Cloudbreak Deployer 
```
   cbd update
```
- Update the `docker-compose.yml` file with new Docker containers that are needed for the `cbd`
```
   cbd regenerate
```
- Check the health and version of the updated `cbd` 
```
   cbd doctor
```
- Start the new version of the `cbd`
```
   cbd start
```

> It will take for a while, because of need to download all the updated docker images for the new version.

## SSH to the hosts

In the current version of Cloudbreak all the nodes have a public IP address and all the nodes are accessible via SSH.
The public IP addresses of a running cluster can be checked on the Cloudbreak UI under the *Nodes* tab.
Only key-based authentication is supported - the public key can be specified when creating a cloud credential.

Each cloud provider has different SSH user. In order to figure out the username you can try it wit the `root` user - that will tell you the correct username.

As an example:

```
ssh root@172.16.252.31
Warning: Permanently added '172.16.252.31' (ECDSA) to the list of known hosts.
Please login with one of the following username rather than the user "root":

  centos                  : Reach the host running the ambari container
  ambari-enter            : Enters the Ambari container
  ambari-log              : Watches the Ambari logs
```

After you figured out the username you can SSH into the host.

```
ssh -i ~/.ssh/private-key.pem USER_NAME@<public-ip>
```

## Accessing HDP client services

The main difference between general HDP clusters and Cloudbreak-installed HDP clusters is that each host runs an Ambari server or agent Docker container and the HDP services will be installed in this container as well.
It means that after `ssh` the client services won't be available instantly, first you'll have to `enter` the ambari-agent container.
Inside the container everything works the same way as expected. In order to do so there are a few options.

*Helper functions*

We have created a few helper functions in order to help operations. Once yoy SSH into the host you should a message as such:
```
You are now logged in to the docker host ...
Getting started with docker:

  docker ps                : Listing running containers
  docker logs -f <ID>      : Star tail -f on container stdout/stderr
  docker exec -it <ID> sh  : Entering the container (instead of ssh)

Helper commands:

  h                        : prints this message
  ambari-enter             : Enters the ambari container
  ambari-log               : Watches the ambari logs
```

You can enter into the container where the HDP services are running using the `ambari-enter ` functions.

*Docker exec*


To check the containers running on the host enter:

```
[cloudbreak@vmhostgroupclient11 ~]$ sudo docker ps
CONTAINER ID   IMAGE                                    COMMAND                CREATED      STATUS      PORTS     NAMES
1098ca778176   sequenceiq/baywatch-client:v1.0.0        "/etc/bootstrap.sh -d" 4 hours ago  Up 4 hours            baywatch-client-14454170059514
f4097c52fda5   sequenceiq/logrotate:v0.5.1              "/start.sh"            4 hours ago  Up 4 hours            logrotate-14454169954830
7b94aedaab30   sequenceiq/docker-consul-watch-plugn:1.0 "/start.sh consul://1" 4 hours ago  Up 4 hours            consul-watch-14454169884044
d8128b001427   sequenceiq/ambari:2.1.2-v2               "/start-agent"         4 hours ago  Up 4 hours            ambari-agent-14454169805924
a8ec90037aaf   swarm:0.4.0                              "/swarm join --addr=1" 4 hours ago  Up 4 hours  2375/tcp  vmhostgroupmaster12-swarm-agent
ef02b43eacee   sequenceiq/consul:v0.5.0-v5              "/bin/start"           4 hours ago  Up 4 hours            vmhostgroupmaster12-consul
```

You should see the `ambari-agent` container running. Copy its id or name and `exec` into the container:

```
[cloudbreak@vmhostgroupclient11 ~]$ sudo docker exec -it ambari-agent-14454169805924 bash
[root@docker-ambari tmp]#
```

Or you can use this one-step command as well:

```
[cloudbreak@vmhostgroupclient11 ~]$ sudo docker exec -it $(sudo docker ps -f=name=ambari-agent -q) bash
[root@docker-ambari tmp]#
```

## Data volumes

The disks that are attached to the instances are automatically mounted to `/hadoopfs/fs1`, `/hadoopfs/fs2`, ... `/hadoopfs/fsN` respectively.
These directories are mounted from the host into the ambari-agent container under the same name so these can be accessed from inside.
It means that if you'd like to move some data to the instances you can use these volumes and the data will be available from the container instantly to work on it.

An `scp` Example:

```
$ scp -qr -i ~/.ssh/private-key.pem ~/tmp/data cloudbreak@<client-node>:/hadoopfs/fs1
$ ssh -i ~/.ssh/private-key.pem cloudbreak@<client-node>
[cloudbreak@vmhostgroupclient11 ~]$ sudo docker exec -it $(sudo docker ps -f=name=ambari-agent -q) bash
[root@docker-ambari tmp]# su hdfs
[hdfs@docker-ambari tmp]# hadoop fs -put /hadoopfs/fs1/data /tmp
[hdfs@docker-ambari tmp]# hadoop fs -ls /tmp
Found 2 items
drwxr-xr-x   - hdfs supergroup          0 2015-10-21 13:46 /tmp/data
drwx-wx-wx   - hive supergroup          0 2015-10-21 08:51 /tmp/hive
```

## Internal hostnames

After a cluster is created with Cloudbreak, the nodes will have internal hostnames like this:

 ```vmhostgroupclient11.node.dc1.consul```

This is because Cloudbreak uses [Consul](https://www.consul.io) to provide DNS services.
It means that you won't see entries to the other nodes inside the `/etc/hosts` file, because nodes are registered inside Consul and the hostnames are resolved by Consul as well.

In the current version the `node.dc1.consul` domain is hardcoded and cannot be changed.

## Accessing Ambari server from the other nodes

Ambari server is registered as a service in Consul, so it can always be accessed through its domain name `ambari-8080.service.consul` from the other ambari containers.
It can be tried by pinging it from one of the `ambari-agent` containers:

```
ping ambari-8080.service.consul
```

## Cloudbreak gateway node

With every Cloudbreak cluster installation there is a special node called *cbgateway* started that won't run an ambari-agent container so it won't run HDP services either.
It can be seen on the Cloudbreak UI among the hostgroups when creating a cluster, but its node count cannot be changed from 1 and it shouldn't be there in the Ambari blueprint.
It is by design because this instance has some special tasks:

- it runs the Ambari server and its database inside Docker containers
- it runs an nginx proxy that is used by the Cloudbreak API to communicate with the cluster securely
- it runs the Swarm manager that orchestrates the Docker containers on the whole cluster
- it runs the Baywatch server that is responsible for collecting the operational logs from the cluster
- it runs a Kerberos KDC container if Kerberos is configured

**Logs**

*Hadoop logs*

Hadoop logs are available from the host and from the container as well in the `/hadoopfs/fs1/logs` directory.

*Ambari logs*

For Ambari logs you can use our helper function, `ambari-log`. For further information please check the *Helper functions* section. Alternatively you watch the Ambari logs on the host instance as well under the ``/hadoopfs/fs1/logs` folder as well.

**Ambari database**

Ambari's database runs on the `cbgateway` node inside a PostgreSQL docker container. To access it SSH to the `gateway` node and run the following command:

```
[cloudbreak@vmcbgateway0 ~]$ sudo docker exec -it ambari_db psql -U postgres
```
