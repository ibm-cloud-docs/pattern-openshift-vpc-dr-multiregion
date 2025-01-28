---

copyright:
  years: 2025
lastupdated: "2025-01-28"


subcollection: pattern-openshift-vpc-dr-multiregion

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Overview
{: #overview}

The {{site.data.keyword.redhat_openshift_notm}} Service on Virtual Private Cloud Multi-Region Disaster Recovery pattern is intended to provide an {{site.data.keyword.IBM}} solution design for deployment on {{site.data.keyword.Bluemix_short}}.

The focus is on using cloud platform capabilities to design a multi-region resilient {{site.data.keyword.redhat_openshift_notm}} on Virtual Private Cloud solution.

The pattern can be used to support business continuity policies and disaster recovery scenarios as well as data residency requirements when two regions within the same country are utilized.

## Pattern Objectives
{: #Pattern-Objective}

The objective of this pattern is to provide a resilient solution design for {{site.data.keyword.redhat_openshift_notm}} Service on a Virtual Private Cloud (VPC) architecture deployment, on {{site.data.keyword.Bluemix_notm}}, that meets disaster recovery requirements for enterprise workloads that require persistent storage. This pattern is intended to:

-   Accelerate and simplify solution design by providing a standard {{site.data.keyword.Bluemix_notm}} deployment architecture reference following the [IBM Architecture Design Framework](/docs/architecture-framework).

-   Provide a prescriptive, enterprise-class solution design, with diagrams, component architecture decisions along with rationale for cloud component selection to meet enterprise requirements.

-   Ensure requirements can be met from a disaster recovery perspective.

This document focuses on leveraging {{site.data.keyword.Bluemix_notm}} platform capabilities to architect a resilient multi-region {{site.data.keyword.redhat_openshift_notm}} environment. The pattern provides for {{site.data.keyword.redhat_openshift_notm}} clusters with backup and restore and disaster recovery at a secondary region within an {{site.data.keyword.Bluemix_notm}} VPC multi-region reference architecture.

This document is an extension of the [{{site.data.keyword.redhat_openshift_notm}} on VPC resiliency](/docs/pattern-openshift-vpc-mz-resiliency?topic=pattern-openshift-vpc-mz-resiliency-overview) pattern which focuses on resilient {{site.data.keyword.redhat_openshift_notm}} clusters within a single region.

## Pattern Details
{: #Pattern-Details}

The Disaster Recovery Pattern for OpenShift deploys containerized enterprise workloads requiring persistent storage within a VPC. It leverages OpenShift clusters, Portworx Enterprise DR Software Defined Storage, network cloud resources, and other cloud services distributed across three availability zones within a primary region, with an additional cluster deployment in a secondary region for disaster recovery. This pattern ensures 99.99% infrastructure availability and provides a recovery point objective (RPO) of 15 minutes or less. Disaster recovery provides the ability to restore and recover an Application/Platform/Service/API after a period of disruption in the event of an unpredictable component failure or regional outage.

Disaster recovery with Portworx requires at least two OpenShift clusters where Portworx Enterprise with Disaster Recovery (PX-DR) is installed and configured for disaster recovery. One of the two clusters is considered the active cluster where your data is primarily stored. All data is then replicated to the standby cluster. If your active cluster becomes unavailable, Portworx Enterprise with Disaster Recovery DR automatically fails over to the standby cluster and makes the standby cluster the new active cluster so that data can continue to be accessed.

NOTE: If you installed Portworx in one of your clusters without the Portworx disaster recovery plan, you must re-install Portworx with the disaster recovery plan so that you can include this cluster in your disaster recovery configuration.

PX-DR Supports HA across Availability Zones, RPO-zero failover across data centers in a metropolitan area and continuous incremental backups across global data centers.

Depending on your cluster requirements, Portworx offers the following two disaster recovery configurations:

[Metro DR](https://docs.portworx.com/portworx-enterprise/operations/operate-kubernetes/disaster-recovery/px-metro){: external}: Your OpenShift clusters are in the same region, such as both clusters are deployed in one or multiple zones of the us-south region. All clusters are configured to use the same Portworx Enterprise cluster and share the same Portworx key-value store. Data is automatically replicated between the clusters because the Portworx storage layer is shared. RPO (Recovery Point Objective) and RTO (Recovery Time Objective) for this configuration is less than 60 seconds.

[Asynchronous DR](https://docs.portworx.com/portworx-enterprise/operations/operate-kubernetes/disaster-recovery/async-dr){: external} Your OpenShift clusters are deployed in different regions, such as us-south and us-east. Each cluster has its own Portworx Enterprise installation and uses a separate Portworx key-value store that is not shared. To replicate data between clusters, you must set up scheduled replication between these clusters. Because of the higher latency and scheduled replication times, the RPO for this scenario might be up to 15 minutes.

This pattern leverages Portworx Asynchronous DR and involves a multi-zone workload cluster in the primary region and employs asynchronous storage replication for Disaster Recovery to a secondary region, maintaining an RPO of 15 minutes. For disaster recovery to the secondary location at least two OpenShift clusters across three availability zones are required, with Portworx SDS installed and configured for disaster recovery.

One cluster will act as the active cluster, where data is primarily stored, while the standby cluster in the secondary disaster recovery region receives replicated data. In the event of an active cluster failure, Portworx automatically fails over to the standby cluster, which then becomes the new active cluster, ensuring continued data access.

*Refer to the* [{{site.data.keyword.redhat_openshift_notm}} on VPC resiliency](/docs/pattern-openshift-vpc-mz-resiliency?topic=pattern-openshift-vpc-mz-resiliency-overview) *pattern for establishing high availability within a region.*

## Pattern Requirements
{: #Patten-Requirements}

Portworx Enterprise Disaster Recovery should be installed on each OpenShift cluster requiring DR within the primary and disaster recovery regions. Portworx asynchronous replication will perform volume replications, and the admin will need to create a migration schedule to migrate applications and volumes between the Portworx primary and the Disaster recovery clusters that are being paired.

With this setup, the Recovery Point Objective (RPO) is 15 minutes, and the Recovery Time Objective (RTO) is less than 60 seconds. Please reference the [Portworx documentation](https://docs.portworx.com/portworx-enterprise/platform/openshift/ocp-ibm-cloud/disaster-recovery){: external} for additional details.
