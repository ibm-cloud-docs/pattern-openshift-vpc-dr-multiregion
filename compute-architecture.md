---

copyright:
  years: 2023
lastupdated: "2023-12-26"

subcollection: pattern-openshift-vpc-dr-multiregion

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Compute Architecture Decisions
{: #Compute-Architecture-Decisions}

The following are compute architecture decisions for this pattern. Refer to the [Architecture decisions for Network {{site.data.keyword.redhat_openshift_notm}} on VPC resiliency](/docs/pattern-openshift-vpc-mz-resiliency?topic=pattern-openshift-vpc-mz-resiliency-networking-architecture) for additional details.

| **Architecture decision**                                     | **Requirement**                                                                 | **Alternative**                                                                                                                                                                                             | **Decision**                                                                                         | **Rationale**                                                                                                                                                                                                                                                                                                                    |
|---------------------------------------------------------------|---------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Compute container platform                                    | Provide a managed container platform                                            | [Kubernetes Service](/docs/containers) [{{site.data.keyword.openshiftlong_notm}}](/docs/openshift?topic=openshift-getting-started) Roll Your Own {{site.data.keyword.redhat_openshift_notm}}                | [{{site.data.keyword.redhat_openshift_notm}} on VPC](/docs/openshift?topic=openshift-getting-started)     | {{site.data.keyword.openshiftlong_notm}} provides a managed container platform with automatic provisioning, backup and updates of master nodes, and etcd storage.                                                                                                                                                                          |
| {{site.data.keyword.redhat_openshift_notm}} on Virtual Private Cloud (VPC) worker nodes | Provide worker node resource capacity for the application and storage workloads | VPC Dedicated Hosts [VPC Virtual Shared](/docs/openshift?topic=openshift-strategy#env_flavors_node)                                                                                    | [VPC Virtual Shared](/docs/openshift?topic=openshift-strategy#env_flavors_node) | Various VPC worker node balanced, compute, GPU, memory and storage optimized flavors are available to choose from based on the workload requirements. x86 compute within an isolated VPC network that can be quickly provisioned and scaled based on load requirements. Compute and Storage will be in a Hyperconverged cluster. |
| OpenShift Cluster Type                                        | Distribute worker nodes across availability zones                               | [Single zone cluster](/docs/openshift?topic=openshift-strategy#sz-single-zone-strategy) [Multizone cluster](docs/openshift?topic=openshift-strategy#mz-strategy) | [Multizone cluster](/docs/openshift?topic=openshift-strategy#mz-strategy)       | Worker nodes (minimum of 6) distributed evenly across three availability zones provide for a 99.99% SLA.                                                                                                                                                                                                                         |
