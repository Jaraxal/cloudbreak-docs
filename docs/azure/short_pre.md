We use the new [Azure ARM](https://azure.microsoft.com/en-us/documentation/articles/resource-group-overview/) in 
order to launch clusters. In order to work we need to create an **Active Directory** application with the configured name and password and adds the permissions that are needed to call the Azure Resource Manager API. Cloudbreak Deployer automates all this for you.

> If you forget to configure these steps you will not able to create any resource with Cloudbreak.