---
copyright:
  years: 2025
lastupdated: "2025-02-14"

subcollection: pattern-openshift-vpc-dr-multiregion

keywords:
# The release that the reference architecture describes

authors:
  - name: Vidula Patel, Rob Talotta

version: 1.0

deployment-url:

docs: https://cloud.ibm.com/docs/pattern-openshift-vpc-dr-multiregion


content-type: reference-architecture
---

{{site.data.keyword.attribute-definition-list}}

# Red Hat OpenShift on VPC multiregion DR
{: #Solution-Architecture}
{: toc-content-type="reference-architecture"}
{: toc-version="1.0"}

This reference architecture is used in a multiregion disaster recovery scenario for a containerized workload with persistent storage and backup requirements. The pattern uses {{site.data.keyword.redhat_openshift_notm}} as the managed container platform and Portworx for software-defined storage (SDS) replication and backup.

## Architecture diagram
{: #architecture-diagram}

![Diagram of an architecture for a {{site.data.keyword.openshiftlong_notm}} on VPC multiregion disaster recovery solution architecture](images/ROKS-VPC-DR-Arch.svg){: caption="{{site.data.keyword.openshiftlong_notm}} on VPC multiregion disaster recovery solution architecture" caption-side="bottom"}

Review the following containers, SDS, and backup disaster recovery components, which corresponds with Figure 1.

**Containers:**

1. Separate hyperconverged compute and storage Red Hat OpenShift clusters are created in primary and DR region accounting for containerized applications and environments that require disaster recovery protection.

   1. To meet the high availability 99.99% SLA 6 worker nodes equally distributed across three availability zones are included within each Red Hat OpenShift cluster in each region.

   2. A minimum of 3 worker nodes with extra local block storage is required so that the Portworx built-in internal key-value database (KVDB) can be set up for high availability. The KVDB stores the state, configuration data, and metadata for your cluster. Your data is automatically replicated across these 3 worker nodes, and you can choose to scale this deployment to replicate data across up to 25 worker nodes.

**Storage and backup:**

2. Portworx Enterprise DR provides Data replication between Red Hat OpenShift source and failover destination clusters in separate regions. Each cluster has its own Portworx Enterprise DR installation and uses a separate Portworx key-value store that is not shared.

3. {{site.data.keyword.block_storage_is_short}} is used as the backing storage for Portworx Enterprise DR and volumes are provisioned on a per availability zone basis.

4. Portworx Enterprise DR async replication is used between the primary and DR site for the DR data replica.

   1. [Setting up disaster recovery with Portworx](/docs/openshift?topic=openshift-storage_portworx_recovery)

5. Portworx Backup is used to backup up cluster resources applications and data from target and source application clusters. Choose the cluster where Portworx Backup is to be installed (this cluster can also be backed up).

6. Add the Red Hat OpenShift clusters that will be backed up to the Portworx Backup service.

7. An {{site.data.keyword.cos_full_notm}} instance and bucket is used as the backup location to the Portworx Backup service.

8. Cross-Region COS plan is used for multiregional protection with concurrent access to protect against entire regional unavailability or outage.

9. To back up a cluster without Portworx Enterprise, you must first install the Portworx storage scheduler [Stork](/docs/openshift?topic=openshift-storage_portworx_backup#px-backup-stork) before you add the cluster to the Portworx Backup service.

**Public connectivity:**

10. {{site.data.keyword.cis_short}} Global Load Balancer feature is used to provide public traffic load balancing between the primary and DR sites.

11. {{site.data.keyword.vpn_vpc_short}} is used to provide secure connectivity from on-premises networks and admin access from anywhere.

**Private connectivity:**

12. Redundant (or single) {{site.data.keyword.dl_short}} connections that are established to the primary and DR sites with Transit Gateway connections.

13. Global Routing is added to the DR region {{site.data.keyword.dl_short}} for resilient private network connectivity outside the local market.

14. Global {{site.data.keyword.tg_short}} in the DR region provides interconnectivity between VPCs for replication traffic between {{site.data.keyword.cloud_notm}} regions.

15. Private {{site.data.keyword.dns_short}} Global Load Balancer or customer provided GLB is used to provide private traffic load balancing between the primary and DR sites.

## Design scope
{: #Design-Scope}

Following the [{{site.data.keyword.arch_framework}}](/docs/architecture-framework?topic=architecture-framework-taxonomy), the {{site.data.keyword.redhat_openshift_notm}} Service on VPC DR pattern covers design considerations and architecture decisions for the following aspects and domains:

- **Compute:** Containers

- **Storage:** Primary storage, backup storage, software-defined storage

- **Networking:** Enterprise connectivity, load balancing, DNS

- **Resiliency:** Backup and restore, disaster recovery

![Illustrates the design scope for a {{site.data.keyword.redhat_openshift_notm}} on VPC multiregion disaster recovery solution architecture](images/heat-map.svg){: caption="{{site.data.keyword.openshiftlong_notm}} VPC multiregion DR design scope" caption-side="bottom"}

The {{site.data.keyword.arch_framework}} provides a structured approach to designing cloud solutions by covering key architectural aspects and domains, helping ensure consistency across enterprise solutions regardless of technology. For more information, see [Introduction to the {{site.data.keyword.arch_framework}}](/docs/architecture-framework?topic=architecture-framework-intro).

## Requirements
{: #Requirements}

The following table outlines key baseline requirements that are essential for most clients to successfully deploy the {{site.data.keyword.redhat_openshift_notm}} Service on VPC DR pattern.

| **Aspect** | **Requirement**  |
|------------|------------------|
| Compute    | Provide a platform for containerized application, storage, and management workloads with adequate compute capacity. |
| Storage    | Provide highly available storage that meets the application performance requirements.  |
| Network    | Enterprise connectivity to customer data centers to provide access to applications from on-premises.  |
|            | Provide network isolation with the ability to separate applications based on attributes such as environment, data classification, public versus internal apps and function.           |
| Resiliency | Provide a containerized platform that supports application availability targets and business continuity policies.                                                       |
|            | Provide highly available compute, storage, network, and other cloud services for a resilient containerized application with persistent storage requirements.            |
|            | Provide a backup solution for containers platform and application data to enable recovery if an unplanned outage occurs.                                                |
|            | Provide highly available storage for containerized databases and stateful applications with cross-region storage replication.                                            |
|            | Provide for an RTO/RPO = 4 hours/15 minutes; expect rollback to original environments no later than specified RTOs.                                                |
|            | Provide public and private enterprise connectivity with failover to a secondary region for disaster recovery Provide a 99.99% SLA on the containerized platform service. |
{: caption="{{site.data.keyword.openshiftlong_notm}} on VPC multiregion DR requirements." caption-side="bottom"}

## Solution components
{: #Solution-Components}

| Category   | Component     | How it's used in the solution   |
|------------|------------------------|------------------------------|
| Compute    | [{{site.data.keyword.openshiftlong_notm}} VPC](/docs/openshift)                                                                | Container platform with worker nodes to support the application, software-defined storage, and backup tool workloads.     |
| Storage    | [{{site.data.keyword.block_storage_is_short}}](https://cloud.ibm.com/infrastructure/provision/storage){: external}                                                | Portworx Enterprise requires {{site.data.keyword.block_storage_is_short}} as the backing storage for cluster worker nodes. Cloud Drives (for VPC Clusters only) can be used to dynamically provision {{site.data.keyword.block_storage_is_short}}for Portworx. |
|            | [Portworx Enterprise](https://cloud.ibm.com/catalog/services/{:portworx-enterprise){: external}                                              | Portworx enterprise provides highly available unified storage across multiple zones for stateful application with Compute and Storage in a Hyperconverged design.   |
|            | [{{site.data.keyword.filestorage_vpc_full}}](https://cloud.ibm.com/infrastructure/provision/fileshare){: external}                                               | File storage offering that provides NFS-based file storage services.    |
|            | [{{site.data.keyword.cos_short}}](https://cloud.ibm.com/objectstorage/create#pricing){: external}                                                    | Backups, Archiving, 2nd offside backup copy, logs (application, operational, and audit logs) [{{site.data.keyword.cos_full_notm}} Smart Tier Cross Regional](https://cloud.ibm.com/objectstorage/create#pricing) or Vault based on the access of the data frequency.   |
| Networking | [{{site.data.keyword.dl_short}}](/docs/dl?topic=dl-get-started-with-ibm-cloud-dl)   | Private network connectivity between VPCs and cloud services.   |
|            | [{{site.data.keyword.cis_short_notm}}](https://cloud.ibm.com/catalog/services/internet-services){: external}                                            | Public DNS resolution.             |
|            | [Global {{site.data.keyword.tg_short}}](https://cloud.ibm.com/interconnectivity/transit/provision){: external}                                             | Connectivity between two different regions for Workload and Management VPCs.   |
|            | [{{site.data.keyword.tg_short}}](https://cloud.ibm.com/interconnectivity/transit/provision){: external}                                                   | Connectivity between Workload and Management VPCs. |
|            | [{{site.data.keyword.vpn_vpc_short}}](/docs/vpc?topic=vpc-vpn-overview){: external}                                                            | Remote access to manage resources in a private network.  |
|            | [{{site.data.keyword.dns_short}}](/docs/dns-svcs?topic=dns-svcs-getting-started){: external}                                             | Private DNS resolution.    |
| Resiliency | [Portworx Enterprise with Disaster Recovery](https://cloud.ibm.com/catalog/services/portworx-enterprise){: external} (**px-dr-enterprise)** | Disaster Recovery Supports HA across Availability Zones, RPO-zero failover across data centers in a metropolitan area and continuous incremental backups across global data centers.  |
|            | [PX-Backup for Kubernetes](https://cloud.ibm.com/catalog/services/px-backup-for-kubernetes){: external}                                     | Portworx Enterprise is the most widely-used and reliable cloud-native storage solution for production workloads and provides high-availability, data protection, and security for containerized applications.                |
|            | [{{site.data.keyword.cis_short}}](https://cloud.ibm.com/catalog/services/internet-services){: external}                                             | Global Load Balancer.             |
|            | [{{site.data.keyword.dns_short}}](/docs/dns-svcs?topic=dns-svcs-getting-started){: external}                                              | Private Global Load Balancer.    |
{: caption="{{site.data.keyword.openshiftlong_notm}} on VPC multiregion DR solution components." caption-side="bottom"}
