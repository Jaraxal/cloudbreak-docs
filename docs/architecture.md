# Architecture

##Cloudbreak Deployer Architecture

- **uaa**: OAuth Identity Server
- **cloudbreak**: the Cloudbreak app
- **periscope**: the Periscope app
- **uluwatu**: Cloudbreak UI
- **sultans**: user management

### System Level Containers

- **consul**: Service Registry
- **registrator**: automatically registers/unregisters containers with consul

##Cloudbreak Application Architecture

Cloudbreak is built on the foundation of cloud providers APIs, Apache Ambari, Docker containers, Swarm and Consul.

###Apache Ambari

The Apache Ambari project is aimed at making Hadoop management simpler by developing software for provisioning, managing, and monitoring Apache Hadoop clusters. Ambari provides an intuitive, easy-to-use Hadoop management web UI backed by its RESTful APIs.

![](https://raw.githubusercontent.com/sequenceiq/cloudbreak/master/docs/images/ambari-overview.png)

####System Administrators
Ambari enables to integrate Hadoop provisioning, management and monitoring capabilities into applications with the Ambari REST APIs.

  1. **Provision a Hadoop Cluster**:
     * Ambari provides a step-by-step wizard for installing Hadoop services across any number of hosts.
     * Ambari handles configuration of Hadoop services for the cluster.
  2. **Manage a Hadoop Cluster**:
     * Ambari provides central management for starting, stopping, and reconfiguring Hadoop services across the entire.
   cluster.
  3. **Monitor a Hadoop Cluster**:
     * Ambari provides a dashboard for monitoring health and status of the Hadoop cluster.
     * Ambari allows to choose between predefined alerts or add your custom ones.

####Ambari Blueprint
Ambari blueprints are a declarative definition of a cluster. With a blueprint, you can specify stack, component
 layout and configurations to materialise a Hadoop cluster instance (via a REST API) without having to use the Ambari
  Cluster Install Wizard.

![](https://raw.githubusercontent.com/sequenceiq/cloudbreak/master/docs/images/ambari-create-cluster.png)

###Docker

Docker is an open platform for developers and sysadmins to build, ship and run distributed applications. Consisting 
of Docker Engine, a portable, lightweight runtime and packaging tool and hub. Docker Hub is a cloud service for sharing 
applications and automating the workflow.

Docker enables apps to be quickly assembled from components and eliminates the
 friction between development, QA and production environments. As a result, IT can ship faster and run the same app, 
 unchanged, on laptops, data center VMs and any cloud.

####The main features of Docker

  1. Lightweight
  2. Portable
  3. Build once
  4. Run anywhere
  5. VM - without the overhead of a VM
    * Each virtualised application includes not only the application and the necessary binaries and libraries, but 
   also an entire guest operating system
    * The Docker Engine container comprises just the application and its dependencies. It runs as an isolated process 
   in userspace on the host operating system, sharing the kernel with other containers.
    ![](https://raw.githubusercontent.com/sequenceiq/cloudbreak/master/docs/images/vm.png)

  6. Containers are isolated
  7. It can be automated and scripted

###Swarm

Docker Swarm is native clustering for Docker. It turns a pool of Docker hosts into a single, virtual host. Swarm serves the standard Docker API.

  * **Distributed container orchestration**: Allows to remotely orchestrate Docker containers on different hosts.
    ![](https://raw.githubusercontent.com/sequenceiq/cloudbreak/master/docs/images/swarm.png)
  * **Discovery services**: Supports different discovery backends to provide service discovery, as such: token (hosted) 
  and file based, etcd, Consul, Zookeeper.
  * **Advanced scheduling**: Swarm will schedule containers on hosts based on different filters and strategies.

###Consul

Consul it is a tool for discovering and configuring services in your infrastructure.

####Key features

  * **Service Discovery**: Clients of Consul can provide a service, such as api or mysql and other clients can use 
  Consul to discover providers of a given service. Using either DNS or HTTP. Applications can easily find the services 
  they depend upon.
  * **Health Checking**: Consul clients can provide any number of health checks, either associated with a given service ("is the webserver returning 200 OK"), or with the local node ("is memory utilization below 90%"). This information can be used by an operator to monitor cluster health, and it is used by the service discovery components to route traffic away from unhealthy hosts.
  * **Key/Value Store**: Applications can make use of Consul's hierarchical key/value store for any number of 
  purposes, including dynamic configuration, feature flagging, coordination, leader election and more. The simple HTTP API makes it easy to use.
  * **Multi Datacenter**: Consul supports multiple datacenters out of the box. This means users of Consul do not have to worry about building additional layers of abstraction to grow to multiple regions.

    ![](https://raw.githubusercontent.com/sequenceiq/cloudbreak/master/docs/images/consul.png)
