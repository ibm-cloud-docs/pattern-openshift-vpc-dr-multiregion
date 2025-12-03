---

copyright:
  years: 2025
lastupdated: "2025-12-03"

subcollection: pattern-openshift-vpc-dr-multiregion

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Architecture decisions for compute
{: #Compute-Architecture-Decisions}

The following are compute architecture decisions for this pattern. For more information, see [Architecture decisions for compute for {{site.data.keyword.redhat_openshift_notm}} on VPC resiliency](/docs/pattern-openshift-vpc-mz-resiliency?topic=pattern-openshift-vpc-mz-resiliency-compute-decisions).


| **Architecture decision** | **Requirement** | **Alternative** | **Decision** | **Rationale** |
|---|---|---|---|---|
| Compute container platform | Provide a managed container platform | - [Kubernetes Service](/docs/containers) \n - [Red Hat OpenShift on IBM Cloud](/docs/openshift?topic=openshift-getting-started) \n Roll Your Own Red Hat OpenShift | [Red Hat OpenShift on VPC](/docs/openshift?topic=openshift-getting-started) | Provides a managed container platform with automatic provisioning, backup and updates of master nodes, and etcd storage. |
| Virtual Private Cloud (VPC) worker nodes | Provide worker node resource capacity for the application and storage workloads | - VPC Dedicated Hosts \n - [VPC Virtual Shared](/docs/openshift?topic=openshift-strategy#env_flavors_node) | [VPC Virtual Shared](/docs/openshift?topic=openshift-strategy#env_flavors_node) | Various VPC worker node, such as balanced, compute, GPU, memory and storage optimized flavors are available to choose from based on the workload requirements. x86 compute within an isolated VPC network that can be quickly provisioned and scaled based on load requirements. Compute and Storage will be in a Hyperconverged cluster. |
| OpenShift cluster Type | Distribute worker nodes across availability zones | - [Single zone cluster](/docs/openshift?topic=openshift-strategy#sz-single-zone-strategy) \n - [Multizone cluster](/docs/openshift?topic=openshift-storage-plan) | [Multizone cluster](/docs/openshift?topic=openshift-storage-plan) | Worker nodes (minimum of 3 in each region) distributed evenly across three availability zones provide for a 99.99% SLA. |
