Cloudbreak was tested against the following versions of [Red Hat Distribution of OpenStack](https://www.rdoproject.org) (RDO):

- Juno
- Kilo
- Liberty
- Mitaka


Cloudbreak requires that the  standard components are installed and configured on OpenStack:

- Keystone V2 or Keystone V3
- Neutron (self-service and provider networking)
- Nova (KVM or Xen hypervisor)
- Glance
- Cinder (optional)
- Heat (optional, but it is highly recommended, since provisioning through native api calls will be deprecated in the future)
