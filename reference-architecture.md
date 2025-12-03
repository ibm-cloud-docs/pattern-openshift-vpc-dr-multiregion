---
copyright:
  years: 2025
lastupdated: "2025-12-03"

subcollection: pattern-openshift-vpc-dr-multiregion

keywords:
# The release that the reference architecture describes

authors:
  - name: Imran Khan, Sharat Guldas

deployment-url:

docs: /docs/pattern-openshift-vpc-dr-multiregion


content-type: reference-architecture
---

{{site.data.keyword.attribute-definition-list}}

# Red Hat OpenShift on VPC multiregion DR
{: #Solution-Architecture}
{: toc-content-type="reference-architecture"}
{: toc-version="1.0"}

This reference architecture is used in a multiregion disaster recovery scenario for a containerized workload with persistent storage and backup requirements. The pattern uses {{site.data.keyword.redhat_openshift_notm}} as the managed container platform and Red Hat OpenShift Data Foundation for software-defined storage (SDS) replication.

## Architecture diagram
{: #architecture-diagram}

![Diagram of an architecture for a {{site.data.keyword.openshiftlong_notm}} on VPC multiregion disaster recovery solution architecture](images/ROKS-VPC-DR-Arch-2-Dec-2025-v3.svg){: caption="{{site.data.keyword.openshiftlong_notm}} on VPC multiregion disaster recovery solution architecture" caption-side="bottom"}

Review the following containers, SDS, and backup disaster recovery components that corresponds with Figure 1:

1. A single hyperconverged compute and storage Red Hat OpenShift cluster is created in primary and DR region accounting for containerized applications and environments that require disaster recovery protection. To meet the high availability 99.99% SLA 3 worker nodes equally distributed across three availability zones are included within each Red Hat OpenShift cluster in each region.

2. OpenShift Data Foundation provides data replication between Red Hat OpenShift source and failover destination clusters in separate regions. Each cluster has its ODF installation with one cluster designated as primary and another as secondary.

5. Red Hat Advanced Cluster Management (ACM) is used to manage ODF clusters and perform application failover and relocation.

10. Public connectivity: {{site.data.keyword.cis_short}} Global Load Balancer feature is used to provide public traffic load balancing between the primary and DR sites.

11. Public connectivity: {{site.data.keyword.vpn_vpc_short}} is used to provide secure connectivity from on-premises networks and admin access from anywhere.

12. Private connectivity: Redundant (or single) {{site.data.keyword.dl_short}} connections that are established to the primary and DR sites with Transit Gateway connections.

13. Private connectivity: Global Routing is added to the DR region {{site.data.keyword.dl_short}} for resilient private network connectivity outside the local market.

14. Private connectivity: Global {{site.data.keyword.tg_short}} in the DR region provides interconnectivity between VPCs for replication traffic between {{site.data.keyword.cloud_notm}} regions.

15. Private connectivity: Private {{site.data.keyword.dns_short}} Global Load Balancer or customer provided GLB is used to provide private traffic load balancing between the primary and DR sites.

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
| Storage    | [{{site.data.keyword.block_storage_is_short}}](/infrastructure/provision/storage){: external}                                                | ODF requires {{site.data.keyword.block_storage_is_short}} as the backing storage for cluster worker nodes. Cloud Drives (for VPC Clusters only) can be used to dynamically provision {{site.data.keyword.block_storage_is_short}}for ODF. |
|            | [ODF](/docs/openshift?topic=openshift-ocs-storage-prep){: external}                                              | ODF provides highly available unified storage across multiple zones for stateful application with Compute and Storage in a Hyperconverged design.   |
|            | [{{site.data.keyword.filestorage_vpc_full}}](/infrastructure/provision/fileshare){: external}                                               | File storage offering that provides NFS-based file storage services.    |
|            | [{{site.data.keyword.cos_short}}](/objectstorage/create#pricing){: external}                                                    | Backups, Archiving, 2nd offside backup copy, logs (application, operational, and audit logs) [{{site.data.keyword.cos_full_notm}} Smart Tier Cross Regional](/objectstorage/create#pricing) or Vault based on the access of the data frequency.   |
| Networking | [{{site.data.keyword.dl_short}}](/docs/dl?topic=dl-get-started-with-ibm-cloud-dl)   | Private network connectivity between VPCs and cloud services.   |
|            | [{{site.data.keyword.cis_short_notm}}](/catalog/services/internet-services){: external}                                            | Public DNS resolution.             |
|            | [Global {{site.data.keyword.tg_short}}](/interconnectivity/transit/provision){: external}                                             | Connectivity between two different regions for Workload and Management VPCs.   |
|            | [{{site.data.keyword.tg_short}}](/interconnectivity/transit/provision){: external}                                                   | Connectivity between Workload and Management VPCs. |
|            | [{{site.data.keyword.vpn_vpc_short}}](/docs/vpc?topic=vpc-vpn-overview){: external}                                                            | Remote access to manage resources in a private network.  |
|            | [{{site.data.keyword.dns_short}}](/docs/dns-svcs?topic=dns-svcs-getting-started){: external}                                             | Private DNS resolution.    |
| Resiliency | [ODF Disaster Recovery](/docs/openshift?topic=openshift-ocs-storage-prep){: external} | Disaster Recovery Supports HA across Availability Zones, RPO-zero failover across data centers in a metropolitan area and continuous incremental backups across global data centers.  |
|            | [{{site.data.keyword.cis_short}}](/catalog/services/internet-services){: external}                                             | Global Load Balancer.             |
|            | [{{site.data.keyword.dns_short}}](/docs/dns-svcs?topic=dns-svcs-getting-started){: external}                                              | Private Global Load Balancer.    |
{: caption="{{site.data.keyword.openshiftlong_notm}} on VPC multiregion DR solution components." caption-side="bottom"}
