---

copyright:
  years: 2023
lastupdated: "2023-12-26"

subcollection: pattern-openshift-vpc-dr-multiregion

keywords:

---

{{site.data.keyword.attribute-definition-list}}

## Networking Architecture Decisions
{: #NetworkingArchitectureDecisions}

The following are networking architecture decisions for this pattern. Please refer to the [Architecture decisions for Network {{site.data.keyword.redhat_openshift_notm:}} on VPC resiliency](https://cloud.ibm.com/docs/pattern-openshift-vpc-mz-resiliency?topic=pattern-openshift-vpc-mz-resiliency-networking-architecture) for additional details.

| **Architecture decision**     | **Requirement**                                | **Alternative**                                                                                                                                                                             | **Decision**                                                                                     | **Rationale**                                                                                                                                                                                         |
|-------------------------------|------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cross-Region VPC Connectivity | Provide VPC to VPC connectivity across regions | [Global Transit Gateway](https://cloud.ibm.com/docs/transit-gateway?topic=transit-gateway-about) [VPN for VPC Site to Site Gateways](https://cloud.ibm.com/docs/vpc?topic=vpc-vpn-overview) | [Global Transit Gateway](https://cloud.ibm.com/docs/transit-gateway?topic=transit-gateway-about) | Provides for private global routing across the IBM Cloud network with built in high availability. Removes the need to manage a distributed network and allows for scaling network capacity on demand. |
