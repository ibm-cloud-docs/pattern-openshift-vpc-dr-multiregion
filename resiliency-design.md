---

copyright:
  years: 2023
lastupdated: "2023-12-26"

subcollection: pattern-openshift-vpc-dr-multiregion

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Resiliency design
{: #Resiliency-Design-Considerations}

Review how to set up asynchronous disaster recovery (DR) for Red Hat OpenShift clusters by using Portworx. Create separate Red Hat OpenShift clusters in the primary and disaster recovery regions to account for the containerized applications and environments that require disaster recovery protection.

## Portworx Enterprise asynchronous disaster recovery
{: #Porworx-Enterprise-DR-Asynchronous-Disaster-Recovery}

The Red Hat OpenShift clusters are deployed in different regions, such as us-south and us-east. Each cluster has its own Portworx installation and uses a separate Portworx key-value store that is not shared. To replicate data between clusters, you must set up scheduled replication between these clusters. Because of the higher latency and scheduled replication times, the RPO for this scenario might be up to 15 minutes.

1. Choose at least two Red Hat OpenShift clusters that are located in different regions. If you have one cluster only, you can still configure this cluster for asynchronous disaster recovery, but Portworx can't do a proper failover until a second cluster is configured.

2. Make sure that all your clusters have sufficient [raw and unformatted block storage](/docs/containers?topic=containers-utilities#manual_block) so that you can build your Portworx storage layer.

3. Review your [options to configure a Portworx key-value store](/docs/containers?topic=containers-storage_portworx_kv_store). Because both clusters are in different regions, each cluster must use its own key-value store. Use the internal Portworx key-value database (KVDB).

4. Enable Portworx [volume encryption](/docs/containers?topic=containers-storage_portworx_encryption) for both of your clusters. The IBM Key Protect credentials are later used by Portworx to encrypt data traffic between the clusters.

5. Follow the instructions to [install Portworx](/docs/containers?topic=containers-storage_portworx_deploy) with the disaster recovery plan in both of your clusters. If you installed Portworx without the disaster recovery plan in one of your clusters already, you must reinstall Portworx in that cluster with the disaster recovery plan. Make sure that you configure the Portworx key-value store that each cluster uses.

6. Follow the [Portworx documentation](https://docs.portworx.com/portworx-enterprise/operations/operate-kubernetes/disaster-recovery){: external} to create a cluster pair, enable disaster recovery mode, and schedule data migrations between your clusters.

For more information on resiliency design considerations, see [{{site.data.keyword.redhat_openshift_notm}} on VPC resiliency](/docs/pattern-openshift-vpc-mz-resiliency?topic=pattern-openshift-vpc-mz-resiliency-overview).

For more information on network disaster recovery, see [Web app cross-region resiliency - Networking design](/docs/pattern-vpc-vsi-cross-region-resiliency?topic=pattern-vpc-vsi-cross-region-resiliency-networking-design).

[Your responsibilities with using {{site.data.keyword.redhat_openshift_notm}} on {{site.data.keyword.cloud_notm}}](/docs/openshift?topic=openshift-responsibilities_iks)
