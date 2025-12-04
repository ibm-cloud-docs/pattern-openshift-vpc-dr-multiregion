---

copyright:
  years: 2025
lastupdated: "2025-12-04"

subcollection: pattern-openshift-vpc-dr-multiregion

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Storage design Considerations
{: #Storage-Design-Considerations}


When designing storage for disaster recovery in Red Hat OpenShift environments, it is critical to evaluate the architectural principles and requirements before selecting a specific solution. This section outlines the key considerations for building a resilient, scalable, and performant storage architecture for containerized workloads.

## Software Defined Storage (SDS)
{: #sds}

SDS is a system that abstracts storage resources from the underlying hardware, allowing Opsnshift to manage and provision storage as a programmable, scalable pool through a software layer. It decouples storage management from specific hardware, enabling the creation of flexible, automated storage devices for applications, often using a Container Storage Interface (CSI) driver. This approach allows for the use of commodity hardware and or cloud drives that provides features like dynamic provisioning, snapshots, and data replication, all managed via OpenShift APIs. Using an SDS solution that is built on an open-source software-defined storage platform such as Ceph provides a unified system for object, file, and block storage.

## OpenShift Native Integration
{: #openshift-native-integration}

When selecting a storage system, your goal should be on identifying a solution that fully integrates with OpenShift APIs and operators, Dynamically provisioning Persistent Volumes (PVs) for container workloads using a CSI driver, lifecycle management, monitoring, and seamless orchestration of containerized workloads. This ensures storage behaves like a native part of the cluster.

## Unified Storage
{: #unified-storage}

While choosing a storage system for your stateful applications, select a single platform that is capable of providing multiple storage types such as block, file, and object, so that different workload requirements can be met without managing multiple underlying storage systems. Unified storage simplifies operations, reduces complexity, and supports diverse enterprise workloads.

## Performance
{: #storage-performance}

To ensure that your application perform optimally under heavy load, select a block storage tier that meets your organization application performance baseline. IBM Cloud provides various block storage performance tiers with differing performance characteristics, select a tier that meets your applications IOPS and throughput needs. When using VPC clusters with VSIs, for performance considerations, use VPC Block storage 10 IOPS for worker nodes.

For more information refer to [Block Storage profile families](https://cloud.ibm.com/docs/vpc?topic=vpc-block-storage-profiles&interface=ui#block-storage-profile-overview)




## Resources
{: #storage-design-resources}

For more information on storage design considerations, see [{{site.data.keyword.redhat_openshift_notm}} on VPC resiliency](/docs/pattern-openshift-vpc-mz-resiliency?topic=pattern-openshift-vpc-mz-resiliency-overview) and [Understanding OpenShift Data Foundation](/docs/openshift?topic=openshift-ocs-storage-prep)
