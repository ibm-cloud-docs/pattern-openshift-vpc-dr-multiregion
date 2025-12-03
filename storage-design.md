---

copyright:
  years: 2025
lastupdated: "2025-12-03"

subcollection: pattern-openshift-vpc-dr-multiregion

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Storage design Considerations
{: #Storage-Design-Considerations}


When you design storage for disaster recovery in {{site.data.keyword.redhat_openshift_notm}} environments by using ODF, consider the following key factors.

- ODF creates a virtualized storage layer, where your app data is replicated for high availability. Because ODF abstracts your underlying storage, you can use ODF to create File, Block, or Object storage claims from the same underlying raw block storage.

- ODF is a software-defined storage (SDS), it aggregates storage that is declared, to the ODF cluster and then creates virtual pool of "persistent storage" that is available to your containers.

- The "storage" can be local to the worker node hype-Converged with compute/storage using solid-state drive storage device or *attached* to the worker node or VPC block storage. The type of storage you use depends on the compute node types, such as VPC vs Bare Metals, and storage model such as external vs internal.

- ODF uses storage volumes in multiples of 3 and replicates your app data across these volumes. The underlying storage volumes that you use for ODF depends on your OCP cluster type. For VPC clusters that use virtual machines, the storage volumes are **Block Storage for VPC storage volumes**.

- For production use or in scenarios with heavy workloads, use dedicated storage nodes for ODF. By separating the operations of storage nodes, you can achieve better performance and scalability for your storage infrastructure.

- When using VPC clusters with VSIs, for performance considerations, use VPC Block storage 10 IOPS for worker nodes.

- In OpenShift Data Foundation (ODF), the replication factor is set to 3 by default. When you add capacity, plan to add storage nodes in multiples of 3.

- Plan to have fewer than nine storage devices per node. This helps prevent potential bottlenecks and enhances the efficiency of data access and retrieval.

- You can optimize the performance of your ODF volumes by matching the type of workload you're running with a suitable IO profile.

- Choose at least two {{site.data.keyword.redhat_openshift_notm}} clusters that are located in different regions.


## Resources
{: #storage-design-resources}

For more information on storage design considerations, see [{{site.data.keyword.redhat_openshift_notm}} on VPC resiliency](/docs/pattern-openshift-vpc-mz-resiliency?topic=pattern-openshift-vpc-mz-resiliency-overview) and [Understanding OpenShift Data Foundation](https://cloud.ibm.com/docs/openshift?topic=openshift-ocs-storage-prep)
