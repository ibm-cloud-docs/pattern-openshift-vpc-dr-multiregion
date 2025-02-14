---

copyright:
  years: 2025
lastupdated: "2025-02-14"

subcollection: pattern-openshift-vpc-dr-multiregion

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Architecture decisions for storage
{: #Storage-Architect-Decisons}

The following are storage architecture decisions for this pattern. For more information, see [Architecture decisions for storage for {{site.data.keyword.redhat_openshift_notm}} on VPC resiliency](/docs/pattern-openshift-vpc-mz-resiliency?topic=pattern-openshift-vpc-mz-resiliency-storage-decisions).

| **Architecture decision** | **Requirement** | **Alternative** | **Decision** | **Rationale** |
|---------------------------|-----------------|-----------------|--------------|---------------|
| Primary storage for worker nodes | Provide highly available storage that meets the application performance requirements | - [{{site.data.keyword.block_storage_is_short}} (5 IOPS)](/docs/vpc?topic=vpc-storage-overview) \n - [{{site.data.keyword.block_storage_is_short}} (10 IOPS)](/docs/vpc?topic=vpc-storage-overview) \n - [{{site.data.keyword.filestorage_vpc_short}}](https://cloud.ibm.com/docs/vpc?topic=vpc-file-storage-vpc-about) \n - [{{site.data.keyword.cos_short}}](/docs/vpc?topic=vpc-connecting-vpc-cos) | [{{site.data.keyword.block_storage_is_short}} (10 IOPS)](/docs/vpc?topic=vpc-storage-overview) | For Portworx across multizone. Also, Cloud drives allow to dynamically provision the Portworx volumes. Portworx requires raw and unformatted block storage. |
| Containers software-defined storage | Provide highly available storage that abstracts data storage for worker nodes and aggregates them into a virtual pool | - [Portworx Enterprise](/docs/openshift?topic=openshift-storage_portworx_about&interface=ui) \n - [Red Hat OpenShift Data Foundation](/docs/openshift?topic=openshift-storage-plan&interface=ui) | [Portworx Enterprise](/docs/openshift?topic=openshift-storage_portworx_about&interface=ui) | For persistent storage across multizone for stateful containerized apps. Portworx provides multiple data access mode like ReadWriteMany (rwx). Portworx provides persistent volume with high availability and disaster recovery as well as data protection for backup and recovery. |
| Portworx architecture | Provides deployment options for Portworx | - Converged \n - Disaggregated | Converged | Each worker node has the role of both computer and storage. Each worker node is connected to a VPC Block Storage Bare metal node, which provides reasonable performance for most workloads. Depending on the type of workloads, the disaggregated architecture might be suitable. |
| Portworx key-value database (KVDB)  | Provide a KVDB solution for Portworx to store the cluster's state, configuration data, and metadata associated with storage volumes and snapshots | - [Internal KVDB for Portworx](/docs/openshift?topic=openshift-storage_portworx_kv_store#portworx-kvdb) \n - {{site.data.keyword.cloud_notm}} Database for etcd \n - Other external KVDB | [Internal KVDB for Portworx](/docs/openshift?topic=openshift-storage_portworx_kv_store#portworx-kvdb) | Use the built-in internal KVDB for your Portworx cluster except when configuring a synchronous disaster recovery setup. For more information, see [Setting up a Databases for etcd service instance](/docs/openshift?topic=openshift-storage_portworx_kv_store). [Deprecation notice for {{site.data.keyword.cloud_notm}} Databases for etcd.](/docs/databases-for-etcd?topic=databases-for-etcd-deprecation) |
{: caption="{{site.data.keyword.openshiftlong_notm}} on VPC multiregion DR storage architecture decisions." caption-side="bottom"}
