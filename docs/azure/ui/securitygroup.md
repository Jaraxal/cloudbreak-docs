#### Security groups

Security group templates are very similar to the [security groups on Azure](https://azure.microsoft.com/en-us/documentation/articles/virtual-networks-nsg/).
**They describe the allowed inbound traffic to the instances in the cluster.**
Currently only one security group template can be selected for a Cloudbreak cluster and all the instances have a 
public IP address so all the instances in the cluster will belong to the same security group.
This may change in a later release.

*Default Security Group*

You can also use the two pre-defined security groups in Cloudbreak.

`only-ssh-and-ssl:` all ports are locked down except for SSH and the selected Ambari Server HTTPS (you can't access Hadoop services 
outside of the virtual network):

* SSH (22)
* HTTPS (443)

*Custom Security Group*

You can define your own security group by adding all the ports, protocols and CIDR range you'd like to use. The rules
 defined here doesn't need to contain the internal rules, those are automatically added by Cloudbreak to the security
  group on Azure.

{!docs/common/ports.md!}

If `Public in account` is checked all the users belonging to your account will be able to use this security group 
template to create clusters, but cannot delete it.

>**NOTE:** The security groups are created on Azure only after the cluster provisioning starts with the selected 
security group template.

>**IMPORTANT:** If you use and existing virtual network and subnet the selected security group will only be applied to the selected Ambari Server node due to the lack of
capability to attach multiple security groups to an existing subnet. If you'd like to open ports for Hadoop you must do it on your existing security group.

![](/images/ui-secgroup_v3.png)
<sub>*Full size [here](/images/ui-secgroup_v3.png).*</sub>