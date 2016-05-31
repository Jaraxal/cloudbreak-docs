#Kerberos Security

> This feature is currently `TECHNICAL PREVIEW`.

Cloudbreak can enable Kerberos security on the cluster. When enabled, Cloudbreak will install an MIT KDC into the cluster
and enable Kerberos on the cluster.

## Enable Kerberos

To enable Kerberos in a cluster, when creating your cluster via the UI, do the following:

1. When in the **Create cluster** wizard, on the **Setup Network and Security** tab, check the **Enable security** option.
2. Fill in the following fields:

| Field | Description |
|---|---|
| Kerberos master key | The master key to use for the KDC. |
| Kerberos admin | The KDC admin username to use for the KDC. |
| Kerberos password | The KDC admin password to use for the KDC. |

> The Cloudbreak Kerberos setup does not contain Active Directory support or any other third party user authentication method. If you
want to use custom Hadoop user, you have to create users manually with the same name on all Ambari containers on each node.

### Testing Kerberos

To run a job on the cluster, you can use one of the default Hadoop users, like `ambari-qa`, as usual.

Once kerberos is enabled you need a `ticket` to execute any job on the cluster. Here's an example to get a ticket:
```
kinit -V -kt /etc/security/keytabs/smokeuser.headless.keytab ambari-qa-sparktest-rec@NODE.DC1.CONSUL
```
Example job:
```java
export HADOOP_LIBS=/usr/hdp/current/hadoop-mapreduce-client
export JAR_EXAMPLES=$HADOOP_LIBS/hadoop-mapreduce-examples.jar
export JAR_JOBCLIENT=$HADOOP_LIBS/hadoop-mapreduce-client-jobclient.jar

hadoop jar $JAR_EXAMPLES teragen 10000000 /user/ambari-qa/terasort-input

hadoop jar $JAR_JOBCLIENT mrbench -baseDir /user/ambari-qa/smallJobsBenchmark -numRuns 5 -maps 10 -reduces 5 -inputLines 10 -inputType ascending
```

### Create Hadoop Users

To create Hadoop users please follow the steps below.

  * Log in via SSH to the Cloudbreak gateway node (IP address is the same as the Ambari UI)

```
kadmin -p [admin_user]/[admin_user]@NODE.DC1.CONSUL (type admin password)
addprinc custom-user (type user password twice)
```

  * Log in via SSH to all other nodes

```
useradd custom-user
```

  * Log in via SSH to one of the nodes

```
su custom-user
kinit -p custom-user (type user password)
hdfs dfs -mkdir input
hdfs dfs -put /tmp/wait-for-host-number.sh input
yarn jar $(find /usr/hdp -name hadoop-mapreduce-examples.jar) wordcount input output
hdfs dfs -cat output/*
```
