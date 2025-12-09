---

copyright:
  years: 2025
lastupdated: "2025-12-03"

subcollection: pattern-openshift-vpc-dr-multiregion

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Architecture decisions resiliency
{: #Resiliency-Architecture-Decisions}

The following are resiliency architecture decisions for this pattern. For more information, see [Architecture decisions for resiliency for {{site.data.keyword.redhat_openshift_notm}} on VPC resiliency](/docs/pattern-openshift-vpc-mz-resiliency?topic=pattern-openshift-vpc-mz-resiliency-resiliency-architecture).

| **Architecture decision** | **Requirement** | **Alternative** | **Decision** | **Rationale** |
|---|---|---|---|---|
| High availability | Ensure availability of resources if there's an outages. Support Service Level Agreement (SLA) targets for application availability | - Single-zone cluster \n - Multi-zone cluster \n - Multiple single-zone clusters in one region \n - Multi zone clusters in two regions | Multi zone clusters in two regions | Regional-DR ensures business continuity during the unavailability of a geographical region, accepting some loss of data in a predictable amount. In the public cloud this would be similar to protecting from a region failure. |
| Software Defined Storage (SDS) | Ensure stateful data is available in secondary region if the primary region is unavailable | - VPC Object Store \n - VPC Block Storage \n - VPC File Storage \n - OpenShift Data Foundation | OpenShift Data Foundation | ODF disaster recovery protects data by replicating the block storage volumes to a secondary region and make those persistant volumes available to applications after failover |
{: caption="{{site.data.keyword.openshiftlong_notm}} VPC multiregion DR resiliency architecture decisions." caption-side="bottom"}
