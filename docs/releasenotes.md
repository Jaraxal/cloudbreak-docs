# Release Notes

The Release Notes document describes new features and fixes incorporated in this version of Cloudbreak.

## Improvements

This release includes the following improvements:

| Feature | Description |
|----|----|
| Designate Ambari Server Host Group (RMP-7026) | When creating a cluster, you can designate in which host group to include Ambari Server. |
| Azure: Support for New Regions (RMP-7028) | Support for Canada East and Canada Central. |

## Fixes & Changes

This release include the following changes:
> Refer to the [Change Log](changelog.md) for a full list of changes.

| Area | Change |
|---|---|
| Azure Cloud Provider | Reduce host name length on Azure in order to be sure that the hostname is under 64 chars. |
| Azure Cloud Provider | Azure VMs now support private deployments (when VMs have private IP only).  |
| Azure Cloud Provider | Cloudbreak no longer uses the same host names for every cluster on Azure.  |
| Azure Cloud Provider | Introduces configurable stack name prefix length in Azure host names: cluster name is generated into the hostname, allowing you to specify how many characters will be included as a prefix in the hostname. |
| Networking | The "all-ports-open" security group was renamed to "UNSECURE-all-services-open" in order to make it more obvious that it is not recommended to use. |
| Networking | The "all-ports-open" security group is not the default anymore. If you would like to make your cluster open, you must explicitly select the "UNSECURE-all-services-open" security group. |

## Technical Preview

This release includes the following Technical Preview features and improvements:

| Feature | Description |
|----|----|
| Custom Images | **Technical Preview** Ability to set custom images. See [Cloud Images](images.md) for more information. |
| AWS: Spot Pricing | **Technical Preview** Support for configuring Spot Price with resource templates. |
| Mesos | **Technical Preview** Support for Mesos cloud provider. See [Mesos](mesos.md) for more information. |
| Kerberos | **Technical Preview** Support for enabling Kerberos on the HDP clusters deployed by Cloudbreak. See [Kerberos](kerberos.md) for more information. |
| Platforms | **Technical Preview** Support for defining Platforms to relate different configurations together. See [Platforms](topologies.md) for more information. |
| Ambari Database | **Technical Preview** Support for using an external database for Ambari. See [Ambari Database](database.md) for more information. |
