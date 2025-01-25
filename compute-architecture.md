---

copyright:
  years: 2023
lastupdated: "2023-12-26"

subcollection: pattern-roks-vpc-dr-multiregion

keywords:

---

{{site.data.keyword.attribute-definition-list}}

## Architecture Decisions
{: #Architecture-Decisions}

The following tables summarize the architecture decisions for the Red Hat OpenShift Kubernetes Service on Virtual Private Cloud Multi-Region Disaster Recovery pattern.

## Compute Architecture Decisions
{: #Compute-Architecture-Decisions}

The following are compute architecture decisions for this pattern. Please refer to the [Architecture decisions for Network Red Hat OpenShift on VPC resiliency](/docs/pattern-openshift-vpc-mz-resiliency?topic=pattern-openshift-vpc-mz-resiliency-networking-architecture) for additional details.

| **Architecture decision**                                     | **Requirement**                                                                 | **Alternative**                                                                                                                                                                                             | **Decision**                                                                                         | **Rationale**                                                                                                                                                                                                                                                                                                                    |
|---------------------------------------------------------------|---------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Compute container platform                                    | Provide a managed container platform                                            | [Kubernetes Service](/docs/containers) [Red Hat OpenShift on VPC](/docs/openshift?topic=openshift-getting-started) Roll Your Own Red Hat OpenShift                | [Red Hat OpenShift on VPC](/docs/openshift?topic=openshift-getting-started)     | Red Hat OpenShift on IBM Cloud provides a managed container platform with automatic provisioning, backup and updates of master nodes, and etcd storage.                                                                                                                                                                          |
| Red Hat OpenShift on Virtual Private Cloud (VPC) worker nodes | Provide worker node resource capacity for the application and storage workloads | VPC Dedicated Hosts [VPC Virtual Shared](/docs/openshift?topic=openshift-strategy#env_flavors_node)                                                                                    | [VPC Virtual Shared](/docs/openshift?topic=openshift-strategy#env_flavors_node) | Various VPC worker node balanced, compute, GPU, memory and storage optimized flavors are available to choose from based on the workload requirements. x86 compute within an isolated VPC network that can be quickly provisioned and scaled based on load requirements. Compute and Storage will be in a Hyperconverged cluster. |
| OpenShift Cluster Type                                        | Distribute worker nodes across availability zones                               | [Single zone cluster](/docs/openshift?topic=openshift-strategy#sz-single-zone-strategy) [Multizone cluster](docs/openshift?topic=openshift-strategy#mz-strategy) | [Multizone cluster](/docs/openshift?topic=openshift-strategy#mz-strategy)       | Worker nodes (minimum of 6) distributed evenly across three availability zones provide for a 99.99% SLA.                                                                                                                                                                                                                         |
