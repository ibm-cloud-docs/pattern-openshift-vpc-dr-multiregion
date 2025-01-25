---

copyright:
  years: 2023
lastupdated: "2023-12-26"

subcollection: pattern-roks-vpc-dr-multiregion

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Storage Design Considerations
{: #Storage-Design-Considerations}

-   Include required storage for Portworx metadata. It is recommended to use the internal Portworx key-value database (KVDB). For more information, see [Setting up the Portworx key-value store](/docs/openshift?topic=openshift-storage_portworx_kv_store). For prerequisites and to learn more about what the key value does, see the [Portworx documentation](https://docs.portworx.com/portworx-enterprise/operations/kvdb-for-portworx/internal-kvdb){: external}.

-   Determine if Storage encryption is needed. There are options for using Hyper Protect Crypto Services or IBM Key Protect. For more information, see [Understanding encryption for Portworx](/docs/openshift?topic=openshift-storage_portworx_encryption).

-   Consider the Storage capacity needed to for the journal devices storage. Journal devices allow Portworx to write logs directly to a local disk on your worker node.

-   Use cloud storage drives with the latest version to dynamically provision the Portworx volumes. If you don’t want to use cloud drives, you must manually attach volumes to worker nodes.

-   Portworx is a software-defined storage (SDS), it aggregates “storage" that is declared, to the Portworx cluster and then creates virtual pool of "persistent storage" that is available to your containers.

-   The "storage" can be *local* to the worker node hype-Converged with compute/storage using solid-state drive storage device or *attached* to the worker node or VPC block storage.

-   For performance considerations Use VPC Block storage 10 IOPS for worker nodes.

-   Portworx requires at least 3 worker nodes with raw and unformatted block storage. Make sure that you spread the nodes evenly across the availability zones.

-   You can optimize the performance of your Portworx volumes by matching the type of workload you're running with a suitable IO profile. Please refer to [Portworx IO Profile](https://docs.portworx.com/portworx-enterprise/concepts/io-profiles\#the-db_remote-profile Au){: external} for additional information.

Choose at least two Red Hat OpenShift clusters that are located in different regions.

Ensure the ROKS clusters have sufficient [raw and unformatted block storage](/docs/openshift?topic=openshift-utilities#manual_block) so that you can build your Portworx storage layer.

Include your [Portworx key-value store](/docs/openshift?topic=openshift-storage_portworx_kv_store). Because both clusters are in different regions, each cluster must use its own key-value store. It is recommended to use the internal Portworx key-value database (KVDB).

Determine Encryption is needed by Enable Portworx [volume encryption]() for your cluster.

Follow the instructions to [install Portworx](/docs/openshift?topic=openshift-storage_portworx_deploy) with the disaster recovery plan in both of your clusters. If you installed Portworx without the disaster recovery plan in one of your clusters already, you must re-install Portworx in that cluster with the disaster recovery plan. Make sure that you configure the Portworx key-value store that each cluster uses.

Follow the [Portworx documentation](https://docs.portworx.com/portworx-enterprise/operations/operate-kubernetes/disaster-recovery){: external} to create a cluster pair, enable disaster recovery mode, and schedule data migrations between your cluster

Refer to the [Red Hat OpenShift on VPC resiliency](/docs/pattern-openshift-vpc-mz-resiliency?topic=pattern-openshift-vpc-mz-resiliency-overview) pattern for additional details on storage design considerations.

Review Portworx storage limitations [here](/docs/openshift?topic=openshift-storage_portworx_plan#portworx_limitations).
