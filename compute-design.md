---

copyright:
  years: 2025
lastupdated: "2025-02-14"

subcollection: pattern-openshift-vpc-dr-multiregion

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Compute design
{: #Compute-Design-Considerations}

The multiregion disaster recovery pattern for {{site.data.keyword.redhat_openshift_notm}} includes high availability compute options, isolation, scalable compute capacity, software-defined storage, and backups to support growing workload demands.

- To meet the high availability 99.99% SLA, 6 worker nodes are equally distributed across three availability zones within each Red Hat OpenShift cluster in each region. For more information, see [Decide how many worker nodes for each cluster](/docs/openshift?topic=openshift-strategy#sizing_workers).

- Portworx Enterprise Disaster Recovery requires at least 3 worker nodes with raw and unformatted block storage. Distribute the worker nodes evenly across the availability zones.

- A minimum of 3 worker nodes with extra local block storage is also required so that the Portworx built-in internal metadata key-value database (KVDB) can be set up for high availability.

For more information on compute design considerations, see [{{site.data.keyword.redhat_openshift_notm}} on VPC resiliency](/docs/pattern-openshift-vpc-mz-resiliency?topic=pattern-openshift-vpc-mz-resiliency-overview).
