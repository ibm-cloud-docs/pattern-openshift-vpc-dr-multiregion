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

This disaster recovery pattern for {{site.data.keyword.redhat_openshift_notm}} deploys containerized enterprise workloads that require persistent storage within a VPC. This pattern uses {{site.data.keyword.redhat_openshift_notm}} clusters, underlying storage system and cloud resources across three availability zones in the primary region. It includes an additional cluster in a secondary region for disaster recovery.

This pattern ensures 99.99% infrastructure availability and provides a recovery point objective (RPO) of 15 minutes or less. Disaster recovery helps ensure that you can restore applications, platforms, services, or APIs after an unexpected component failure or regional outage.

Disaster recovery with storage replication requires at least two {{site.data.keyword.redhat_openshift_notm}} clusters where storage system and app pods are installed and configured for disaster recovery. One of the two clusters is considered the active cluster where your data is primarily stored. All data is then replicated to the standby cluster. If your active cluster becomes unavailable, the data and application fails over to the standby cluster. The standby cluster is the new active cluster so that you can continue to access data.

There are different types of disaster recovery deployments depending on your recovery time and data loss requirements.

### Metro-DR
{: #metro-dr}

Metro-DR ensures business continuity during the unavailability of a data centre with no data loss. In the public cloud these would be similar to protecting from an Availability Zone failure.

### Regional-DR
{: #regional-dr}

Regional-DR ensures business continuity during the unavailability of a geographical region, accepting some loss of data in a predictable amount. In the public cloud this would be similar to protecting from a region failure.

### Disaster Recovery with stretch cluster
{: #dr-stretch-cluster}

Stretch cluster solution ensures business continuity with no-data loss disaster recovery protection with storage based synchronous replication in a single OpenShift cluster, stretched across two data centres with low latency and one arbiter node.

Zone failure in Metro-DR and region failure in Regional-DR is usually expressed using the terms, **Recovery Point Objective (RPO)** and **Recovery Time Objective (RTO)**.

**RPO** is a measure of how frequently you take backups or snapshots of persistent data. In practice, the RPO indicates the amount of data that will be lost or need to be re-entered after an outage.

**RTO** is the amount of downtime a business can tolerate. The RTO answers the question, “How long can it take for our system to recover after we are notified of a business disruption?”


This pattern uses asynchronous disaster recovery (DR) with a multi-zone workload cluster in the primary region. It replicates storage asynchronously to a secondary region, ensuring at least 5-minute recovery point objective (RPO). For DR to the secondary location, you need at least two {{site.data.keyword.redhat_openshift_notm}} clusters across three availability zones.

One cluster acts as the active cluster where data is primarily stored. The standby cluster in the secondary disaster recovery region receives replicated data. If an active cluster failure occurs, **Red Hat Advanced Cluster Management** provides controls to fail over the application to the secondary cluster, which becomes the new active cluster to help ensure continued data access.

The intent of this guide is to detail the disaster recovery steps and commands necessary to be able to failover an application from one OpenShift Container Platform cluster to another and then relocate the same application to the original primary cluster.

This guide will focus on providing step by step instructions to deploy a Regional-DR enabled OpenShift Cluster using a storage solution that supports asynchronous data replication between two regions.
