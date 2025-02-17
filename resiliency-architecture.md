---

copyright:
  years: 2025
lastupdated: "2025-02-14"

subcollection: pattern-openshift-vpc-dr-multiregion

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Architecture decisions resiliency
{: #Resiliency-Architecture-Decisions}

The following are resiliency architecture decisions for this pattern. For more information, see [Architecture decisions for resiliency for {{site.data.keyword.redhat_openshift_notm}} on VPC resiliency](/docs/pattern-openshift-vpc-mz-resiliency?topic=pattern-openshift-vpc-mz-resiliency-resiliency-architecture).

| **Architecture decision** | **Requirement** | **Alternative** | **Decision** | **Rationale** |
|---------------------------|-----------------|-----------------|--------------|---------------|
| Software-defined storage with disaster recovery | Provides disaster recovery features and capabilities | [Portworx Enterprise DR](https://cloud.ibm.com/catalog/services/portworx-enterprise){: external} | [Portworx Enterprise DR](https://cloud.ibm.com/catalog/services/portworx-enterprise){: external} | Portworx SDS Asynchronous replication between two regions for Disaster Recovery. The Portworx Enterprise DR license is activated on both the primary Portworx Cluster Bare Metal and the Disaster Recovery Portworx Cluster Bare Metal. |
| Private network disaster recovery | Network disaster recovery capability in secondary region to meet recovery time objective (RTO) recovery point objective (RPO) requirements | - Single {{site.data.keyword.dl_full_notm}} Dedicated \n - Single {{site.data.keyword.dl_full_notm}} Connect \n - Two {{site.data.keyword.dl_full_notm}} Dedicated \n - Two {{site.data.keyword.dl_full_notm}} Connect | Single {{site.data.keyword.dl_full_notm}} Connect with Global Routing | Provides a cost-effective and flexible connection into a second region, with metered and unmetered billing options and a resilient network path across the {{site.data.keyword.IBM}} backbone. |
| Global load balancing | Distribute application requests across regions or fail over application to an alternative region if failure in the primary region occurs   | - [{{site.data.keyword.cis_short}}](https://cloud.ibm.com/catalog/services/internet-services){: external} (public) \n - [{{site.data.keyword.dns_short}}](/docs/dns-svcs?topic=dns-svcs-getting-started) (private) | - [{{site.data.keyword.cis_short}}](https://cloud.ibm.com/catalog/services/internet-services){: external} (public) \n - [{{site.data.keyword.dns_short}}](/docs/dns-svcs?topic=dns-svcs-getting-started){: external} (private) | IBM Cloud Internet Services provides a global load balancer that can distribute user requests across regions in multiregion, web app deployments. |
| Portworx backup cluster | Help ensure availability of a backup solution if a workload cluster failure occurs | - Separate Cluster for Portworx Backup \n - Use workload cluster | Separate cluster for Portworx Backup | Use a separate cluster for Portworx Backup to ensure that your backups remain available. If the backup solution is installed on the workload cluster and that cluster goes down, your backups are lost too. |
| Storage secret store | Needed on both the primary and DR cluster to store credentials | {{site.data.keyword.cos_full_notm}} | {{site.data.keyword.cos_full_notm}} | Secret Store is configured to store credentials on both primary cluster and DR cluster on {{site.data.keyword.cos_short}}. |
{: caption="{{site.data.keyword.openshiftlong_notm}} on VPC multiregion DR resiliency architecture decisions." caption-side="bottom"}
