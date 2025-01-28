---

copyright:
  years: 2023
lastupdated: "2023-12-26"

subcollection: pattern-openshift-vpc-dr-multiregion

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Compute Design Considerations
{: #Compute-Design-Considerations}

The multi-region disaster recovery pattern for {{site.data.keyword.redhat_openshift_notm}} includes compute options that are highly available, properly isolated, provide capacity for the application, software defined storage and backups, and can handle increased workload demands.

-   To meet the high availability 99.99% SLA 6 worker nodes equally distributed across three availability zones are included within each OpenShift cluster in each region. Refer to [decide how many worker nodes for each cluster for](/docs/openshift?topic=openshift-strategy#sizing_workers) additional details.

-   Portworx Enterprise Disaster Recovery requires at least 3 worker nodes with raw and unformatted block storage. Distribute the worker nodes evenly across the availability zones.

-   A minimum of 3 worker nodes with additional local block storage is also required so that the Portworx built-in internal metadata key-value database (KVDB) can be set up for high availability.

Refer to the [{{site.data.keyword.redhat_openshift_notm}} on VPC resiliency](/docs/pattern-openshift-vpc-mz-resiliency?topic=pattern-openshift-vpc-mz-resiliency-overview) pattern for additional details on compute design considerations.
