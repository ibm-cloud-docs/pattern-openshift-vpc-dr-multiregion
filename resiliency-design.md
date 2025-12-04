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

The availability of Red Hat OpenShift clusters depends on the availability of the control plane components the main nodes and etcd database, and the availability of the worker nodes where the applications are deployed.

For workloads deployed on Red Hat OpenShift on IBM Cloud clusters, IBM Cloud is responsible for the availability of the cluster’s control plane. When a Red Hat OpenShift on IBM Cloud cluster is created, IBM Cloud sets up highly available main nodes and control plane components, automatically backs up etcd data, and provides automated disaster recovery for the control plane. IBM Cloud users are responsible for setting up highly available worker nodes. The following are deployment options for Red Hat OpenShift on IBM Cloud clusters:



### Single zone cluster
{: #single-zone-cluster}

A single control plane running in only one availability zone. To improve application availability and to allow failover if one worker node is not available in the cluster, add additional worker nodes to the single zone cluster. The workload becomes unavailable if there's a zonal outage. Single zone clusters are suitable for only transient or noncritical workloads.

### Multizone cluster
{: #multi-zone-cluster}

Create a multizone cluster to distribute workloads across multiple worker nodes and zones, and protect against zone failures with hosts, networks, or apps. If resources in one zone go down, the cluster workloads continue to run in the other zones. Multizone clusters provide high availability and the following more benefits:

- Easily manage worker nodes of the same flavor (CPU, memory, virtual or physical) with worker pools.
- Guard against zone failure by spreading nodes evenly across the selected multizones and by using anti-affinity pod deployments for applications.
- Decrease costs by using multizone clusters instead of duplicating the resources in a separate cluster.
- Benefit from automatic load balancing across apps with the multizone load balancer (MZLB) that is set up automatically in each zone of the cluster. This was the selected deployment model for the e-commerce use case as it provides the required service level availability and workload management capabilities.

### Cross regional active to active cluster deployment
{: #cross-region-active-active}

For solutions that require high availability and capacity management, two active clusters can be deployed with the workload distributed by using a global load balancer. if there's a cluster failure in one region, the second cluster can be scaled to manage the full workload. For the e-commerce use case, it was decided that multiregion deployment was not required with the additional costs factored into the decision.


## Resiliency considerations for {{site.data.keyword.redhat_openshift_notm}}

OpenShift disaster recovery (DR) requires a comprehensive strategy that leverages cloud infrastructure while addressing specific OpenShift components such as application state, data synchronization, and networking. Key considerations involve defining clear Recovery Time Objectives (RTO) and Recovery Point Objectives (RPO), choosing the right architecture, and implementing automation for failover and testing.

### Compute Resiliency

#### High Availability

High availability (HA) is a core discipline in an IT infrastructure to keep your apps up and running, even after a partial or full site failure. The main purpose of high availability is to eliminate potential points of failures in an IT infrastructure. Consider deploying clusters with at least 3 worker nodes evenly distributed across multiple zones. When you are designing your OpenShift cluster infrastructure, first understand what your organization’s uptime availability is in terms of percentage, such as 99.9% or 99.99%, and then take an HA approach that meets your requirements.


#### Cross Region

Disasters cause a workload to go down despite attempts to make it highly available. The worst disasters have widespread consequences, which means that affected workloads might require recovery in a different region altogether.

A proper disaster recovery plan helps protect workloads against region wide failures such as power grid failures, natural calamities, fire etc, that may severely impact your application’s availability. To ensure that your application continues to meet your organization’s goal in case of regional failures, deploy your {{site.data.keyword.redhat_openshift_notm}} clusters in at least two different cloud regions. You may choose active-active or active-passive DR strategy, depending on your RTO and RPO requirements.

**Deployment Model**

Ensure that you design your OpenShift Infrastructure architecture to protect your worker and/or storage nodes against region level failures such as power grid failures, natural calamities, fire etc. Select one of the cluster compute deployment models below after you have defined your applications RTO.

-	Active-Passive

The OpenShift cluster in the primary region handles traffic, while another OpenShift cluster in a secondary region is on standby. Data is replicated from primary to secondar cluster asynchronously. This approach is simpler and more cost-effective, but has a higher RTO due to failover time.

- Active-Active

Both OpenShift clusters in primary and secondary regions are active and handle traffic at the same time, with data synchronized between them. This offers the lowest RTO and RPO but is the most complex and costly to implement. Your application and data architecture must be designed to support this deployment model.

- Active-Inactive (Cold DR)

The secondary cluster is offline until a failure occurs in the primary region, this model offers the lowest operational cost but it has highest RTO as the secondary cluster must be built from scratch and the data is usually restored from a backup.


## Storage Resiliency

Before you can decide on which storage solution is the right for disaster recovery of your Red Hat® OpenShift® on IBM Cloud® clusters, you must understand the IBM Cloud infrastructure, your app requirements, the type of data that you want to store, and how often you want to access this data.

**Decide whether your data must be permanently stored.**

- Persistent storage: Data stored on persistent storage persists even when the container, the worker node, or the cluster is removed. Use persistent storage in for stateful application, core business data or data that must be available due to legal requirements, such as a defined retention period. Persistent storage is also a good option for auditing. You will mostly require disaster recover for volumes or data stored on persistant volumes.

- Non-persistent storage: Your data can be removed when the container, the worker node, or the cluster is removed. Non-persistent storage is typically used for logging information, such as system logs or container logs, development testing, or when you want to access data from the host's file system.

- High Availability (HA):  Storage resources must be designed to tolerate failures, including node crashes, network issues, disk failures or in case of public cloud, zone failures. A properly designed HA strategy ensures workloads remain operational with minimal downtime, through zonal replication, failover mechanisms, and automated recovery.

- Disaster Recovery (DR): To protect your OpenShift stateful application workloads from regional failures, the design must support asynchronously replicating stateful data to a different region that is not affected by similar outages. A storage replication method ensures that your applications can failover and access the same data that was in primary region so that business can continue with minimal impact.

## Network

- Network reachability between regions: Ensure you are using Global Transit Gateway to establish communication between your VPC clusters in different regions.

- Global Load Balancing: For seamless failover between primary and secondary OpenShift clusters, consider deploying Global Load Balancers.

- Application load balancers: If there are multiple instances of your application pods across different worker nodes within a zone, create a network service in OpenShift with Application Load Balancer.

- DNS: Create a DNS record that points to the IP address of the Global Load Balancer. In case an application is failed over to secondary region, your end-users can continue to access the application using the same DNS record.

## Automation and Orchestration
Automating failover and failback processes significantly reduces RTO and minimizes manual errors. Tools like Red Hat Advanced Cluster Management (RHACM) can manage multi-cluster environments and automate these operations.

## Service Level Agreements
{: #slas}

IBM Cloud provides Service Level Agreement eligibility based on these deployment strategies. To meet the Tier 3 SLA of 99.99%, deploy a multizone cluster and distribute the common workload across 2 or more worker in each of three separate Availability Zones for a minimum total of six worker nodes. A Tier 1 SLA of 99.9% is provided for all other configurations including the minimum sizing for single zone and multizone clusters.

For more information, see [IBM Cloud Service Level Agreements](https://www.ibm.com/support/customer/csol/terms/?id=i126-9268&lc=en#detail-document).

For more information on resiliency design considerations, see [{{site.data.keyword.redhat_openshift_notm}} on VPC resiliency](/docs/pattern-openshift-vpc-mz-resiliency?topic=pattern-openshift-vpc-mz-resiliency-overview). For more information on network disaster recovery, see [Web app cross-region resiliency - Networking design](/docs/pattern-vpc-vsi-cross-region-resiliency?topic=pattern-vpc-vsi-cross-region-resiliency-networking-design). Also, review [Your responsibilities with using {{site.data.keyword.redhat_openshift_notm}} on {{site.data.keyword.cloud_notm}}](/docs/openshift?topic=openshift-responsibilities_iks).
