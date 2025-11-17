---

copyright:
  years: 2025
lastupdated: "2025-10-28"

subcollection: pattern-openshift-vpc-dr-multiregion

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Architecture decisions for networking
{: #NetworkingArchitectureDecisions}

The following are networking architecture decisions for this pattern. For more information, see [Architecture decisions for networking for {{site.data.keyword.redhat_openshift_notm}} on VPC resiliency](/docs/pattern-openshift-vpc-mz-resiliency?topic=pattern-openshift-vpc-mz-resiliency-networking-architecture).

| **Architecture decision** | **Requirement** | **Alternative** | **Decision** | **Rationale** |
|---------------------------|-----------------|-----------------|--------------|---------------|
| Cross-region VPC connectivity | Provide VPC to VPC connectivity across regions | - [Global {{site.data.keyword.tg_short}}](/docs/transit-gateway?topic=transit-gateway-about) \n - [{{site.data.keyword.vpn_vpc_short}} Site to Site Gateways](/docs/vpc?topic=vpc-vpn-overview) | [Global {{site.data.keyword.tg_short}}](/docs/transit-gateway?topic=transit-gateway-about) | Provides for private global routing across the {{site.data.keyword.Bluemix_notm}} network with built in high availability. No need to manage a distributed network and allows for scaling network capacity on demand. |
{: caption="{{site.data.keyword.openshiftlong_notm}} VPC multiregion DR networking architecture decisions." caption-side="bottom"}
