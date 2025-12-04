---

copyright:
  years: 2025
lastupdated: "2025-12-04"

subcollection: pattern-openshift-vpc-dr-multiregion

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Compute design
{: #Compute-Design-Considerations}

The multiregion disaster recovery pattern for {{site.data.keyword.redhat_openshift_notm}} includes high availability compute options, isolation, scalable compute capacity, software-defined storage, and backups to support growing workload demands.

Consider the following points when deciding on compute resources for implementing disaster recovery for your {{site.data.keyword.redhat_openshift_notm}}.

## Cluster Types
{: #cluster-types}

There are two methods of deploying storage clusters for OpenShift Continer pLatform, each with its advantages and disadvantages, select the one that meets your performance, operational management and cost requirements.

### Hyperconverged
{: #hyperconverged}

In this model, the same worker nodes are used to deploy both application pods and storage system pods. The performance would be better with this approach as the storage system components run on the same compute nodes as the application pods. In addition, this model simplifies the management and day to day operations but at the same time it makes scaling inflexible because you cannot independently scale compute or storage nodes. Adding a node increases both compute and storage capacity simultaneously even though that may not have been your intention.

### Partial Converged
{: #partial-converged}

In this model application pods and storage system pods are deployed into their own compute or storage worker nodes. This model provides greater flexibility as you can independently scale compute or storage nodes based on actual demand, thereby optimize the resource utilization and control costs. At the same time, this model may introduce latency between the application pods and storage as the storage system components run on different storage nodes. In addtion, you need to consider the cost factor due to additional licenses needed for separate storage nodes.

## Performance
{: #compute-performance}

One of the important aspects of OpenShift cluster design is performance. A rightly sized cluster will ensure application reliability, scalability, and a good end-user experience.

- Ensure the worker and/or storage node profile you select for have sufficient vCPUs and RAM to meet the resource demands of your workloads.

- Consider peak usage and burst capacity to prevent throttling or out of memory issues.

- In this guide, we will deploy containers and storage solution on the same set of worker nodes (Hyperconverged) in each OpenShift cluster and in this case the minimum recommended VSI profile for each node in the clusters is 16 vCPUs and 64 GB RAM.

- You can also use a VSI size of 8 vCPUs and 32 GB RAM for storage system worker nodes but you must taint the nodes so that the application pods are not deployed on these storage worker nodes.


For more information on compute design considerations, see [{{site.data.keyword.redhat_openshift_notm}} on VPC resiliency](/docs/pattern-openshift-vpc-mz-resiliency?topic=pattern-openshift-vpc-mz-resiliency-overview).
