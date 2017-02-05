#Kerberos Security

> This feature is part of `TECHNICAL PREVIEW`.

Cloudbreak Supports 3 different way for provision Kerberos enabled clusters all the possibilities described above:

## Create new MIT Kerberos at provisioning time

Cloudbreak supports using Kerberos security on the cluster. When creating a cluster, provide your Kerberos configuration details, and Cloudbreak will install an MIT KDC and enable Kerberos on the cluster.

### Enable Kerberos

To enable Kerberos on a cluster, do the following when creating your cluster via Cloudbreak web UI:

1. In the **Create cluster** wizard, in the **Setup Network and Security** tab, check the **Enable security** option and select **Create New MIT Kerberos**.
2. Fill in the following fields:

| Field | Description |
|---|---|
| Kerberos master key | The master key to use for the KDC. |
| Kerberos admin | The KDC admin username to use for the KDC. |
| Kerberos password | The KDC admin password to use for the KDC. |

![](/images/kerberosnew.png)
<sub>*Full size [here](/images/kerberosnew.png).*</sub>

#### Testing Kerberos

To run a job on the cluster, you can use one of the default Hadoop users, like `ambari-qa`.
Once kerberos is enabled, you need a `ticket` to execute any job on the cluster. 

Here's an example of how to get a ticket:
```
kinit -V -kt /etc/security/keytabs/smokeuser.headless.keytab ambari-qa-sparktest-rec@NODE.DC1.CONSUL
```
Here is an example job:
```java
export HADOOP_LIBS=/usr/hdp/current/hadoop-mapreduce-client
export JAR_EXAMPLES=$HADOOP_LIBS/hadoop-mapreduce-examples.jar
export JAR_JOBCLIENT=$HADOOP_LIBS/hadoop-mapreduce-client-jobclient.jar

hadoop jar $JAR_EXAMPLES teragen 10000000 /user/ambari-qa/terasort-input

hadoop jar $JAR_JOBCLIENT mrbench -baseDir /user/ambari-qa/smallJobsBenchmark -numRuns 5 -maps 10 -reduces 5 -inputLines 10 -inputType ascending
```

## Use your Existing MIT Kerberos server with a Cloudbreak provisioned cluster.

Cloudbreak supports using Kerberos security on the cluster with an existing MIT Kerberos. When creating a cluster, provide your Kerberos configuration details, and Cloudbreak will automatically extend your blueprint configuration with the defined properties. [Setup an exiting MIT KDC](https://docs.hortonworks.com/HDPDocuments/Ambari-2.2.0.0/bk_Ambari_Security_Guide/content/_use_an_exisiting_mit_kdc.html)

To enable Kerberos on a cluster, do the following when creating your cluster via Cloudbreak web UI:

1. In the **Create cluster** wizard, in the **Setup Network and Security** tab, check the **Enable security** option and select **Use Existing MIT Kerberos**.
2. Fill in the following fields:

| Field | Description |
|---|---|
| Kerberos Password | The KDC admin password to use for the KDC. |
| Existing Kerberos Principal | The KDC principal in your existing MIT KDC. |
| Existing Kerberos URL | The location of your existing MIT KDC. |
| Use Tcp Connection | The connection type for your existing MIT KDC (default is **UDP**). |
| Existing Kerberos Realm | The realm in your existing MIT KDC. |

![](/images/kerberosmit.png)
<sub>*Full size [here](/images/kerberosmit.png).*</sub>

To enable Kerberos on a cluster, do the following when creating your cluster via Cloudbreak shell:

| Parameter | Description |
|---|---|
| --kerberosPassword | The KDC admin password to use for the KDC. |
| --kerberosPrincipal | The KDC principal in your existing MIT KDC. |
| --kerberosUrl | The location of your existing MIT KDC. |
| --kerberosTcpAllowed | The connection type for your existing MIT KDC (default is **UDP**). |
| --kerberosRealm | The realm in your existing MIT KDC. |

## Use your Existing Active Directory with a Cloudbreak provisioned cluster.

Cloudbreak supports using Kerberos security on the cluster with an existing Active Directory. When creating a cluster, provide your Kerberos configuration details, and Cloudbreak will automatically extend your blueprint configuration with the defined properties. [Setup an Active Directory for Kerberos](https://docs.hortonworks.com/HDPDocuments/Ambari-2.2.0.0/bk_Ambari_Security_Guide/content/_use_an_existing_active_directory_domain.html)

### Enable Kerberos

To enable Kerberos on a cluster, do the following when creating your cluster via Cloudbreak web UI:

1. In the **Create cluster** wizard, in the **Setup Network and Security** tab, check the **Enable security** option and select **Use Existing Active Directory**.
2. Fill in the following fields:

| Field | Description |
|---|---|
| Kerberos Password | The KDC admin password to use for the KDC. |
| Existing Kerberos Principal | The KDC principal in your existing MIT KDC. |
| Existing Kerberos URL | The location of your existing MIT KDC. |
| Use Tcp Connection | The connection type for your existing MIT KDC (default is **UDP**). |
| Existing Kerberos Realm | The realm in your existing MIT KDC. |
| Existing Kerberos Ldap AD Url | The url of the existing secure ldap (eg. ldaps://10.1.1.5). |
| Existing Kerberos AD Container DN | Active Directory User container for principals. For example, "OU=Hadoop,OU=People,dc=apache,dc=org". |

![](/images/kerberosad.png)
<sub>*Full size [here](/images/kerberosad.png).*</sub>

To enable Kerberos on a cluster, do the following when creating your cluster via Cloudbreak shell:

| Parameter | Description |
|---|---|
| --kerberosPassword | The KDC admin password to use for the KDC. |
| --kerberosPrincipal | The KDC principal in your existing MIT KDC. |
| --kerberosUrl | The location of your existing MIT KDC. |
| --kerberosTcpAllowed | The connection type for your existing MIT KDC (default is **UDP**). |
| --kerberosRealm | The realm in your existing MIT KDC. |
| --kerberosLdapUrl | The url of the existing secure ldap (eg. ldaps://10.1.1.5). |
| --kerberosContainerDn | Active Directory User container for principals. For example, "OU=Hadoop,OU=People,dc=apache,dc=org". |

## Create Hadoop Users

To create Hadoop users, follow the steps below.

  * Log in via SSH to the node where the Ambari Server is (IP address is the same as the Ambari UI) and run:

```
kadmin -p [admin_user]/[admin_user]@NODE.DC1.CONSUL (type admin password)
addprinc custom-user (type user password twice)
```

  * Log in via SSH to all other nodes and, on each node, run:

```
useradd custom-user
```

  * Log in via SSH to one of the nodes and run:

```
su custom-user
kinit -p custom-user (type user password)
hdfs dfs -mkdir input
hdfs dfs -put /tmp/wait-for-host-number.sh input
yarn jar $(find /usr/hdp -name hadoop-mapreduce-examples.jar) wordcount input output
hdfs dfs -cat output/*
```
