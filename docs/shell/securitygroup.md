#### Security groups

Security group templates are very similar to the [security groups on the AWS Console](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html). **They describe the allowed 
inbound traffic to the instances in the cluster.** Currently only one security group template can be selected for a 
Cloudbreak cluster and all the instances have a public IP address so all the instances in the cluster will belong to 
the same security group. This may change in a later release.

*Default Security Group*

You can also use the two pre-defined security groups in Cloudbreak.

`only-ssh-and-ssl:` all ports are locked down except for SSH and the selected Ambari Server HTTPS (you can't access Hadoop services outside of the VPC):

* SSH (22)
* HTTPS (443)

*Custom Security Group*

You can define your own security group by adding all the ports, protocols and CIDR range you'd like to use. The rules
 defined here doesn't need to contain the internal rules, those are automatically added by Cloudbreak to the security group on AWS.

>**IMPORTANT** 443 needs to be there in every security group otherwise Cloudbreak won't be able to communicate with the 
provisioned cluster.

```
securitygroup create --name my-security-group --description "sample description" --rules 0.0.0.0/0:tcp:443,8080,9090;10.0.33.0/24:tcp:1234,1235
```

If `--publicInAccount` is true, all the users belonging to your account will be able
 to use this template to create clusters, but cannot delete it.

>**NOTE** The security groups are created on AWS only after the cluster provisioning starts with the selected security group template.

You can check whether the security group was created successfully
```
securitygroup list
``` 
