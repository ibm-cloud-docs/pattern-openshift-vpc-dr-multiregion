---

copyright:
  years: 2025
lastupdated: "2025-12-05"


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

This disaster recovery pattern for {{site.data.keyword.redhat_openshift_notm}} deploys containerized enterprise workloads that require persistent storage within a VPC. This pattern uses {{site.data.keyword.redhat_openshift_notm}} clusters, OpenShift Data Foundation, which is a software-defined storage (SDS) solution, and cloud resources across three availability zones in the primary region. It includes an additional cluster in a secondary region for disaster recovery.

This pattern ensures 99.99% infrastructure availability and provides a recovery point objective (RPO) of 15 minutes or less. Disaster recovery helps ensure that you can restore applications, platforms, services, or APIs after an unexpected component failure or regional outage.

Disaster recovery with ODF requires at least two {{site.data.keyword.redhat_openshift_notm}} clusters where ODF Operators are installed and configured for disaster recovery. One of the two clusters is considered the active cluster where your data is primarily stored. All data is then replicated to the standby cluster. If your active cluster becomes unavailable, ODF Disaster Recovery automatically fails over to the standby cluster. The standby cluster is the new active cluster so that you can continue to access data.

OpenShift Data Foundation’s DR capability enables DR across multiple Red Hat OpenShift Container Platform clusters, and is categorized as follows:

### Metro-DR
{: #metro-dr}

Metro-DR ensures business continuity during the unavailability of a data centre with no data loss. In the public cloud these would be similar to protecting from an Availability Zone failure.

### Regional-DR
{: #regional-dr}

Regional-DR ensures business continuity during the unavailability of a geographical region, accepting some loss of data in a predictable amount. In the public cloud this would be similar to protecting from a region failure.

### Disaster Recovery with stretch cluster
{: #dr-stretch-cluster}

Stretch cluster solution ensures business continuity with no-data loss disaster recovery protection with OpenShift Data Foundation based synchronous replication in a single OpenShift cluster, stretched across two data centres with low latency and one arbiter node.

Zone failure in Metro-DR and region failure in Regional-DR is usually expressed using the terms, **Recovery Point Objective (RPO)** and **Recovery Time Objective (RTO)**.

**RPO** is a measure of how frequently you take backups or snapshots of persistent data. In practice, the RPO indicates the amount of data that will be lost or need to be re-entered after an outage.

**RTO** is the amount of downtime a business can tolerate. The RTO answers the question, “How long can it take for our system to recover after we are notified of a business disruption?”


This pattern uses ODF asynchronous disaster recovery (DR) with a multi-zone workload cluster in the primary region. It replicates storage asynchronously to a secondary region, ensuring at least 5-minute recovery point objective (RPO). For DR to the secondary location, you need at least two {{site.data.keyword.redhat_openshift_notm}} clusters across three availability zones, with ODF Operator installed and configured.

One cluster acts as the active cluster where data is primarily stored. The standby cluster in the secondary disaster recovery region receives replicated data. If an active cluster failure occurs, **RHACM** provides controls to fail over the application to the secondary cluster, which becomes the new active cluster to help ensure continued data access.


The intent of this guide is to detail the disaster recovery steps and commands necessary to be able to failover an application from one OpenShift Container Platform cluster to another and then relocate the same application to the original primary cluster.

This guide will focus on providing step by step instructions to deploy a Regional-DR enabled OpenShift Cluster using OpenShift Data Foundation as Storage System.


## Components of a OpenShift Data Foundation Regional DR
{: #Components-openshift-odf-dr}


## Component diagram
{: #component-diagram}

![Diagram explaining various components of OFD in a VPC multiregion disaster recovery solution architecture](images/ODF_components_2_Dec_2025.svg){: caption="{{site.data.keyword.openshiftlong_notm}} on VPC multiregion disaster recovery solution architecture with OFD Storage System" caption-side="bottom"}

This pattern contains the following components of Red Hat OpenShift Data Foundation.
1. Red Hat Advanced Cluster Management (RHACM):
    RHACM consists of two major parts, Hub Cluster and Managed Clusters

    1. Hub cluster where Red Hat Advanced Cluster Management (RHACM) for Kubernetes operator are installed.
    2. Primary managed cluster where OpenShift Data Foundation is running and this will be considered active site for application.
    3. Secondary managed cluster where OpenShift Data Foundation is running and this will be considered passive site for application.

2. OpenShift Data Foundation:
      OpenShift Data Foundation provides the ability to provision and manage storage for stateful applications in an OpenShift Container Platform cluster.

      OpenShift Data Foundation is backed by Ceph as the storage provider, whose lifecycle is managed by Rook in the OpenShift Data Foundation component stack. Ceph-CSI provides the provisioning and management of Persistent Volumes for stateful applications.

3. OpenShift DR:
      OpenShift DR is a set of orchestrators to configure and manage stateful applications across a set of peer OpenShift clusters which are managed using RHACM and provides cloud-native interfaces to orchestrate the life-cycle of an application’s state on Persistent Volumes.
      OpenShift DR is split into three components:

      1. ODF Multicluster Orchestrator: Installed on the multi-cluster control plane (Hub cluster), it orchestrates configuration and peering of OpenShift Data Foundation clusters for Metro and Regional DR relationships.

      2. OpenShift DR Hub Operator: Automatically installed as part of ODF Multicluster Orchestrator installation on the hub cluster to orchestrate failover or relocation of DR enabled applications.

      3. OpenShift DR Cluster Operator: Automatically installed on each managed cluster that is part of a Metro and Regional DR relationship to manage the lifecycle of all PVCs of an application.

For more details refer to [Components of Region-DR solution](https://docs.redhat.com/en/documentation/red_hat_openshift_data_foundation/4.19/html-single/configuring_openshift_data_foundation_disaster_recovery_for_openshift_workloads/index#components-of-regional-dr-solution_rdr).
