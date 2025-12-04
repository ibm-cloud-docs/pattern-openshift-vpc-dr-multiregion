---

copyright:
  years: 2025
lastupdated: "2025-12-04"

subcollection: pattern-openshift-vpc-dr-multiregion

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Resiliency design
{: #Resiliency-Design-Considerations}

High availability is built into IBM Cloud VPC Red Hat OpenShift clusters and can be increased to meet availability requirements. To increase availability, consider deployment strategies for the number of nodes within a cluster and the node placement within a cluster across availability zones.


## Cluster availability
{: #Cluster availability}

The availability of Red Hat OpenShift clusters depends on the availability of the control plane components, like the the master nodes and etcd database, and also on the availability of the worker nodes where the applications are deployed.

For workloads deployed on Red Hat OpenShift on IBM Cloud clusters, IBM Cloud is responsible for the availability of the cluster’s control plane. When a Red Hat OpenShift on IBM Cloud cluster is created, IBM Cloud sets up highly available master nodes and control plane components, automatically backs up etcd data, and provides automated disaster recovery for the control plane. IBM Cloud users are responsible for setting up highly available worker nodes. The following are deployment options for Red Hat OpenShift on IBM Cloud clusters:


### Single zone cluster
{: #single-zone-cluster}

A single control plane running in only one availability zone. To improve application availability and to allow failover if one worker node is not available in the cluster, add additional worker nodes to the single zone cluster. The workload becomes unavailable if there's a zonal outage. Single zone clusters do not protect workloads from zone or region failures and are suitable for only transient or noncritical workloads.

### Multizone cluster
{: #multi-zone-cluster}

Create a multizone cluster to distribute workloads across multiple worker nodes and zones, and protect against zone failures with hosts, networks, or apps. If resources in one zone go down, the cluster workloads continue to run in the other zones. Multizone clusters provide high availability and the following benefits:

- Easily manage worker nodes of the same flavor (CPU, memory, virtual or physical) with worker pools.
- Guard against zone failure by spreading nodes evenly across the selected multizones and by using anti-affinity pod deployments for applications.
- Decrease costs by using multizone clusters instead of duplicating the resources in a separate cluster.
- Benefit from automatic load balancing across apps with the multizone load balancer (MZLB) that is set up automatically in each zone of the cluster.


### Cross region disaster recovery
{: #cross-region-active-active}

A proper disaster recovery plan helps protect workloads against region wide failures such as power grid failures, natural calamities, fire etc, that may severely impact your application’s availability. To ensure that your application continues to meet your organization’s goal in case of regional failures, deploy your {{site.data.keyword.redhat_openshift_notm}} clusters in at least two different cloud regions.

The stateful application will be deployed in the primary region and the application data must be asynchronously replicated to the secondary region. If the primary region fails, the stateful applications will failover to the OpenShift cluster in the secondary and continue to access the replocated data.

OpenShift disaster recovery (DR) requires a comprehensive strategy that leverages cloud infrastructure while addressing specific OpenShift components such as application state, data synchronization, and networking. Key considerations involve defining clear Recovery Time Objectives (RTO) and Recovery Point Objectives (RPO), choosing the right architecture, and implementing automation for failover and testing.


**Deployment Models for OpenShift DR** Choose one of the following cluster deployment models after you have defined your applications RTO.

-	Active-Passive

The OpenShift cluster in the primary region handles traffic, while another OpenShift cluster in a secondary region is on standby. Data is replicated from primary to secondar cluster asynchronously. This approach is simpler and more cost-effective, but has a higher RTO due to failover time.

- Active-Active

Both OpenShift clusters in primary and secondary regions are active and handle traffic at the same time, with data synchronized between them. This offers the lowest RTO and RPO but is the most complex and costly to implement. Your application and data architecture must be designed to support this deployment model.

- Active-Inactive (Cold DR)

The secondary cluster is offline until a failure occurs in the primary region, this model offers the lowest operational cost but it has highest RTO as the secondary cluster must be built from scratch and the data is usually restored from a backup.


## Storage Resiliency
{: #storage-resiliency}

Before you can decide on which storage solution is the right for disaster recovery of your Red Hat® OpenShift® on IBM Cloud® clusters, you must understand the IBM Cloud infrastructure, your app requirements, the type of data that you want to store, and how often you want to access this data.

**Decide whether your data must be permanently stored.**

- Persistent storage: Data stored on persistent volumes persists even when the container, the worker node, or the cluster is removed. Use persistent storage in for stateful application, core business data or data that must be available due to legal requirements, such as a defined retention period. Persistent storage is also a good option for auditing. You will mostly require disaster recovery for data stored that is stored on persistant volumes.

- Non-persistent storage: Your data can be removed when the container, the worker node, or the cluster is removed. Non-persistent storage is typically used for logging information, such as system logs or container logs, development testing, or when you want to access data from the host's file system.

### Storage High Availability (HA)
{: #storage-ha}

Storage resources must be designed to tolerate failures, including node crashes, network issues, disk failures or in case of public cloud, zone failures. A properly designed HA strategy ensures workloads remain operational with minimal downtime, through zonal replication, failover mechanisms, and automated recovery. In a multizone storage HA design, your stateful data will only be protected against zonal failures.

### Storage Disaster Recovery (DR)
{: #storage-dr}

To protect your OpenShift stateful application workloads from regional failures, the design must support asynchronously replicating stateful data in persistant volumes to a different region. A storage replication method ensures that your applications can failover and access the same data that from the secondary region so that businesses can continue to access the applications with minimal impact.

## Network Resiliency
{: #network-resiliency}

- Network reachability between regions: Ensure you are using Global Transit Gateway to establish communication between your VPC clusters in different regions.

- Global Load Balancing: For seamless failover between primary and secondary OpenShift clusters, consider deploying Global Load Balancers.

- Application load balancers: If there are multiple instances of your application pods across different worker nodes within a zone, create a network service in OpenShift with Application Load Balancer.

- DNS: Create a DNS record that points to the IP address of the Global Load Balancer. In case an application is failed over to secondary region, your end-users can continue to access the application using the same DNS record.

## Automation and Orchestration
{: #automation-orchestration}

Automating failover and failback processes significantly reduces RTO and minimizes manual errors. Tools like Red Hat Advanced Cluster Management (RHACM) can manage multi-cluster environments and automate these operations.

## Service Level Agreements
{: #slas}

IBM Cloud provides Service Level Agreement eligibility based on these deployment strategies. To meet the Tier 3 SLA of 99.99%, deploy a multizone cluster and distribute the common workload across 2 or more worker in each of three separate Availability Zones for a minimum total of six worker nodes. A Tier 1 SLA of 99.9% is provided for all other configurations including the minimum sizing for single zone and multizone clusters.

For more information, see [IBM Cloud Service Level Agreements](https://www.ibm.com/support/customer/csol/terms/?id=i126-9268&lc=en#detail-document).

For more information on resiliency design considerations, see [{{site.data.keyword.redhat_openshift_notm}} on VPC resiliency](/docs/pattern-openshift-vpc-mz-resiliency?topic=pattern-openshift-vpc-mz-resiliency-overview). For more information on network disaster recovery, see [Web app cross-region resiliency - Networking design](/docs/pattern-vpc-vsi-cross-region-resiliency?topic=pattern-vpc-vsi-cross-region-resiliency-networking-design). Also, review [Your responsibilities with using {{site.data.keyword.redhat_openshift_notm}} on {{site.data.keyword.cloud_notm}}](/docs/openshift?topic=openshift-responsibilities_iks).
