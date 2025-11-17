---

copyright:
  years: 2025
lastupdated: "2025-10-28"


subcollection: pattern-openshift-vpc-dr-multiregion

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Overview
{: #overview}

The {{site.data.keyword.openshiftlong}} Service Virtual Private Cloud (VPC) multiregion Disaster Recovery pattern provides an {{site.data.keyword.IBM}} solution design for deployment on {{site.data.keyword.Bluemix_notm}}.

The focus is on using cloud platform capabilities to design a resilient multiregion {{site.data.keyword.redhat_openshift_notm}} VPC solution.

This pattern supports business continuity, disaster recovery (DR), and data residency requirements when you use two regions within the same country.

## Pattern objectives
{: #Pattern-Objective}

This pattern provides a resilient solution design for the {{site.data.keyword.redhat_openshift_notm}} service on an {{site.data.keyword.Bluemix_notm}} VPC architecture deployment that meets disaster recovery requirements for enterprise workloads that require persistent storage. This pattern is intended to:

- Accelerate and simplify solution design by providing a standard {{site.data.keyword.Bluemix_notm}} deployment architecture reference that follows the [{{site.data.keyword.IBM_notm}} Architecture Design Framework](/docs/architecture-framework).

- Provide a prescriptive, enterprise-class solution design, with diagrams, component architecture decisions along with rationale for cloud component selection to meet enterprise requirements.

- Ensure that requirements can be met from a disaster recovery perspective.

You can use {{site.data.keyword.Bluemix_notm}} platform capabilities to design a resilient multiregion {{site.data.keyword.redhat_openshift_notm}} environment. This pattern enables {{site.data.keyword.redhat_openshift_notm}} clusters with backup, restore, and disaster recovery in a secondary region within an {{site.data.keyword.Bluemix_notm}} VPC multiregion reference architecture.

This document is an extension of the [{{site.data.keyword.redhat_openshift_notm}} on VPC resiliency](/docs/pattern-openshift-vpc-mz-resiliency?topic=pattern-openshift-vpc-mz-resiliency-overview) pattern, which focuses on resilient {{site.data.keyword.redhat_openshift_notm}} clusters within a single region.

## Pattern details
{: #Pattern-Details}

This disaster recovery pattern for {{site.data.keyword.redhat_openshift_notm}} deploys containerized enterprise workloads that require persistent storage within a VPC. This pattern uses {{site.data.keyword.redhat_openshift_notm}} clusters, Portworx Enterprise DR, which is a software-defined storage (SDS) solution, and cloud resources across three availability zones in the primary region. It includes an extra cluster in a secondary region for disaster recovery.

This pattern ensures 99.99% infrastructure availability and provides a recovery point objective (RPO) of 15 minutes or less. Disaster recovery helps ensure that you can restore applications, platforms, services, or APIs after an unexpected component failure or regional outage.

Disaster recovery with Portworx requires at least two {{site.data.keyword.redhat_openshift_notm}} clusters where Portworx Enterprise with Disaster Recovery (PX-DR) is installed and configured for disaster recovery. One of the two clusters is considered the active cluster where your data is primarily stored. All data is then replicated to the standby cluster. If your active cluster becomes unavailable, Portworx Enterprise with DR automatically fails over to the standby cluster. The standby cluster is the new active cluster so that you can continue to access data.

If you install Portworx in a cluster without the [Portworx disaster recovery pricing plan](https://cloud.ibm.com/catalog/services/portworx-enterprise), reinstall Portworx with the DR plan so that you can include this cluster in your disaster recovery configuration.
{: note}

Portworx disaster recovery supports high availability across availability zones, RPO-zero failover across data centers in a metropolitan area, and continuous incremental backups across global data centers.

Depending on your cluster requirements, Portworx offers the following two disaster recovery configurations:

[Metro DR](https://docs.portworx.com/portworx-enterprise/operations/disaster-recovery/px-metro){: external}
:   Your {{site.data.keyword.redhat_openshift_notm}} clusters are in the same region, such as both clusters are deployed in one or multiple zones of the `us-south` region. All clusters are configured to use the same Portworx Enterprise cluster and share a Portworx key-value store. Data is automatically replicated between the clusters because the Portworx storage layer is shared. RPO (Recovery Point Objective) and RTO (Recovery Time Objective) for this configuration is less than 60 seconds.

[Asynchronous DR](https://docs.portworx.com/portworx-enterprise/operations/disaster-recovery/async-dr){: external}
:   Your {{site.data.keyword.redhat_openshift_notm}} clusters are deployed in different regions, such as `us-south` and `us-east`. Each cluster has its own Portworx Enterprise installation and uses a separate Portworx key-value store that is not shared. To replicate data between clusters, you must set up scheduled replication between these clusters. Because of the higher latency and scheduled replication times, the RPO for this scenario might be up to 15 minutes.

This pattern uses Portworx asynchronous disaster recovery (DR) with a multi-zone workload cluster in the primary region. It replicates storage asynchronously to a secondary region, ensuring a 15-minute recovery point objective (RPO). For DR to the secondary location, you need at least two {{site.data.keyword.redhat_openshift_notm}} clusters across three availability zones, with Portworx SDS installed and configured.

One cluster acts as the active cluster where data is primarily stored. The standby cluster in the secondary disaster recovery region receives replicated data. If an active cluster failure occurs, Portworx automatically fails over to the standby cluster, which becomes the new active cluster to help ensure continued data access.

To establish high availability within a region, see the pattern [{{site.data.keyword.redhat_openshift_notm}} on VPC resiliency](/docs/pattern-openshift-vpc-mz-resiliency?topic=pattern-openshift-vpc-mz-resiliency-overview).

## Pattern requirements
{: #Patten-Requirements}

Install Portworx Enterprise Disaster Recovery on each {{site.data.keyword.redhat_openshift_notm}} cluster that requires DR within the primary and disaster recovery regions. Portworx asynchronous replication performs volume replications. The admin creates a schedule to migrate applications and volumes between the Portworx primary and disaster recovery clusters that are paired.

With this setup, the RPO is 15 minutes and the RTO is less than 60 seconds. For more information, see [Portworx documentation](https://docs.portworx.com/portworx-enterprise/operations/disaster-recovery){: external}.
