---

copyright:
  years: 2025
lastupdated: "2025-12-03"

subcollection: pattern-openshift-vpc-dr-multiregion

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Architecture decisions for storage
{: #Storage-Architect-Decisons}

The following are storage architecture decisions for this pattern. For more information, see [Architecture decisions for storage for {{site.data.keyword.redhat_openshift_notm}} on VPC resiliency](/docs/pattern-openshift-vpc-mz-resiliency?topic=pattern-openshift-vpc-mz-resiliency-storage-decisions).

| **Architecture decision** | **Requirement** | **Alternative** | **Decision** | **Rationale** |
|---------------------------|-----------------|-----------------|--------------|---------------|
| Primary storage for worker nodes | Provide highly available storage that meets the application performance requirements | - [{{site.data.keyword.block_storage_is_short}} (5 IOPS)](/docs/vpc?topic=vpc-storage-overview) \n - [{{site.data.keyword.block_storage_is_short}} (10 IOPS)](/docs/vpc?topic=vpc-storage-overview) \n - [{{site.data.keyword.filestorage_vpc_short}}](/docs/vpc?topic=vpc-file-storage-vpc-about) \n - [{{site.data.keyword.cos_short}}](/docs/vpc?topic=vpc-connecting-vpc-cos) | [{{site.data.keyword.block_storage_is_short}} (10 IOPS)](/docs/vpc?topic=vpc-storage-overview) | For OpenShift Data Foundation OSD disks across multizone. Also, since OpenShift Data Foundation requires raw and unformatted block storage, cloud drives allow to dynamically provision the OSD Volumes. |
| Containers software-defined storage | Provide highly available storage that abstracts data storage for worker nodes and aggregates them into a virtual pool | - Portworx Enterprise \n - [Red Hat OpenShift Data Foundation](/docs/openshift?topic=openshift-storage-plan&interface=ui) | [Red Hat OpenShift Data Foundation](/docs/openshift?topic=openshift-storage-plan&interface=ui) | For persistent storage across multizone and asynchronous replication across multiple regions for stateful containerized apps. OFD provides multiple data access mode like ReadWriteMany (rwx). OFD provides persistent volume with high availability and disaster recovery. |
| ODF Architecture  | Provides deployment options for OFD | - Local Block Storage \n - Remote Dynamic (VPC) Block Storage \n - Database for etcd | Remote Dynamic (VPC) Block Storage | For lower storage needs or if using Virtual Server instances, VPC Dynamic storage can be a convenient and cost-effective option as the block volumes are provisioned as and when required. However, for large storage requirements, a bare metal cluster, or high-performance storage with low network latency, local storage would be more suitable.
