## Infrastructure Templates

After your GCP account is linked to Cloudbreak you can start creating resource templates that describe your clusters'
infrastructure:

- templates
- networks
- security groups

When you create one of the above resources, **Cloudbreak does not make any requests to GCP. Resources are only created
on GCP after the `create cluster` button has pushed.** These templates are saved to Cloudbreak's database and can be
reused with multiple clusters to describe the infrastructure.

#### Templates

Templates describe the **instances of your cluster** - the instance type and the attached volumes. A typical setup is
 to combine multiple templates in a cluster for the different types of nodes. For example you may want to attach multiple
 large disks to the datanodes or have memory optimized instances for Spark nodes.

The instance templates can be configured on the **manage templates** panel on the Cloudbreak Dashboard.

If `Preemptible` is checked then the template will be [preemptible](https://cloud.google.com/compute/docs/instances/preemptible)

If `Public in account` is checked all the users belonging to your account will be able to use this resource to create clusters, but cannot delete it.
