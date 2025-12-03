---

copyright:
  years: 2025
lastupdated: "2025-12-03"

subcollection: pattern-openshift-vpc-dr-multiregion

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Compute design
{: #Compute-Design-Considerations}

The multiregion disaster recovery pattern for {{site.data.keyword.redhat_openshift_notm}} includes high availability compute options, isolation, scalable compute capacity, software-defined storage, and backups to support growing workload demands.

- Open Data Foundation Disaster Recovery configuration requires at least 3 worker nodes with unformatted dynamic block storage. Distribute these worker nodes evenly across the availability zones in each region. For more information, see [Decide how many worker nodes for each cluster](/docs/openshift?topic=openshift-strategy#sizing_workers).

- In this guide, we will deploy containers and ODF on the same set  of worker nodes in each managed cluster and the minimum required VSI size for managed clusters is 16 vCPUs and 64 GB RAM.

      -	You can also use a VSI size of 8 vCPUs and 32 GB RAM for ODF storage worker nodes but you must taint the nodes so that the application pods are not deployed on these storage worker nodes.

- For ACM cluster, we will deploy a ROKS cluster consisting of 3 worker nodes with 8 vCPUs and 32 GB RAM, spread across 3 availability zone in a single region.

For more information on compute design considerations, see [{{site.data.keyword.redhat_openshift_notm}} on VPC resiliency](/docs/pattern-openshift-vpc-mz-resiliency?topic=pattern-openshift-vpc-mz-resiliency-overview).
