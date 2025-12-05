---

copyright:
  years: 2025
lastupdated: "2025-12-05"

subcollection: pattern-openshift-vpc-dr-multiregion

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Storage design Considerations
{: #Storage-Design-Considerations}


When designing storage for disaster recovery in Red Hat OpenShift environments, it is critical to evaluate the architectural principles and requirements before selecting a specific solution. This section outlines the key considerations for building a resilient, scalable, and performant storage architecture for containerized workloads.

## Storage types

### IBM Cloud Block Storage
{: #block-storage}

IBM Cloud Block Storage on Kubernetes offers persistent storage for containerized applications, ensuring data durability and scalability. It seamlessly integrates with Kubernetes clusters, providing dynamic provisioning, snapshots, and encryption. This enables efficient management and utilization of storage resources within the Kubernetes environment, enhancing application performance and reliability. For more information, see [Block Storage for VPC documentation](/docs/openshift?topic=openshift-vpc-block).

Review the following use cases for IBM Cloud Block Storage:

-	Stateful Applications: The IBM Cloud Block Storage is ideal for deploying stateful applications like databases (for example, MySQL, PostgreSQL) in Kubernetes clusters.

-	Data Analytics Workloads: When running data analytics workloads, you often need to process and store large volumes of data.

### IBM Cloud Object Storage
{: #cos}

IBM Cloud's Object Storage plug-in optimizes Kubernetes for seamless data management with IBM Cloud Object Storage. Using IBM's robust storage service, it simplifies integration with cloud-native apps, offering distributed, geo-redundant storage. This pattern caters efficiently to diverse Kubernetes storage needs, enabling effortless provisioning, management, and dynamic resource allocation, streamlining administration. This type of storage volume is not suitable for write-intensive workloads, random write operations, incremental data updates, or transaction databases. For more information surrounding IBM Cloud Object Storage, see the following Setting up [IBM Cloud Object Storage](/docs/openshift?topic=openshift-storage-cos-understand).

Review the following use cases for IBM Cloud Object Storage:

-	Data Backup and Archival: IBM Cloud Object Storage facilitates data backup and archival, crucial for scenarios like Kubernetes-based e-commerce apps. Automated backups ensure data durability and quick recovery.

-	Media Storage for Content Delivery: Content delivery applications, such as video streaming platforms, require efficient and scalable media storage. With the IBM Cloud Object Storage pattern, you can store media assets like videos, images, and audio files. When a user requests media content, Kubernetes can retrieve it from IBM Cloud Object Storage and deliver it seamlessly to the user, ensuring a smooth streaming experience.

-	Cross-Region Data Replication: IBM Cloud Object Storage supports cross-region data replication, essential for global businesses ensuring data redundancy and disaster recovery. Data synchronization between Kubernetes clusters minimizes downtime if there are regional failures.

### IBM Cloud Databases
{: #cloud-dbs}

IBM Cloud Databases simplify storage deployment on IBM Cloud Red Hat OpenShift environment, that uses its services for seamless experiences in provisioning, scaling, and maintenance. Supporting PostgreSQL, MySQL, Redis, and MongoDB, it offers automated backups, high availability, and security, enabling focus on applications rather than infrastructure. For more information, see [IBM Cloud Database Services](https://www.ibm.com/products/cloud-databases).

Review the following use cases for IBM Cloud Databases:

-	Microservices Applications: IBM Cloud Databases suits microservice apps, providing isolated databases for each microservice. For instance, in e-commerce, deploy separate PostgreSQL databases for user accounts, products, and orders, ensuring data isolation, scalability, and fault tolerance.

-	Scalable Web Applications: IBM Cloud Databases automatically scale for variable web app traffic. For example, during flash sales, Kubernetes scales the database to handle increased traffic.

## Software Defined Storage (SDS)
{: #sds}

SDS is a system that abstracts storage resources from the underlying hardware, allowing OpenShift to manage and provision storage as a programmable, scalable pool through a software layer. It decouples storage management from specific hardware, enabling the creation of flexible, automated storage devices for applications, often using a Container Storage Interface (CSI) driver. This approach allows for the use of commodity hardware and or cloud drives that provides features like dynamic provisioning, snapshots, and data replication, all managed via OpenShift APIs. Using an SDS solution that is built on an open-source software-defined storage platform such as Ceph provides a unified system for object, file, and block storage.

One of the prime examples of Software Defined Storage Solution is Red Hat OpenShift Data Foundation (ODF), which is designed to provide persistent, scalable, and highly available storage for containerized applications running on Red Hat OpenShift

### Key Features

- Unified Storage Platform: Supports block, file, and object storage for diverse application needs.

- Scalability & High Availability: Built on Ceph technology to deliver elastic scaling and fault tolerance.

- Integrated with OpenShift: Provides native integration for Kubernetes-based deployments.

- Data Services: Includes features like encryption, compression, and replication for enterprise-grade reliability.

- Hybrid Cloud Ready: Enables data mobility across on-premises and cloud environments.


## Components of a OpenShift Data Foundation Regional DR
{: #Components-openshift-odf-dr}


## Component diagram
{: #component-diagram}

![Diagram explaining various components of OFD in a VPC multiregion disaster recovery solution architecture](images/ODF_components_2_Dec_2025.svg){: caption="{{site.data.keyword.openshiftlong_notm}} on VPC multiregion disaster recovery solution architecture with OFD Storage System" caption-side="bottom"}

This solution contains the following components of Red Hat OpenShift Data Foundation.
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
      OpenShift DR is split into three components

      1. ODF Multicluster Orchestrator: Installed on the multi-cluster control plane (Hub cluster), it orchestrates configuration and peering of OpenShift Data Foundation clusters for Metro and Regional DR relationships.

      2. OpenShift DR Hub Operator: Automatically installed as part of ODF Multicluster Orchestrator installation on the hub cluster to orchestrate failover or relocation of DR enabled applications.

      3. OpenShift DR Cluster Operator: Automatically installed on each managed cluster that is part of a Metro and Regional DR relationship to manage the lifecycle of all PVCs of an application.
      

For more details refer to [Components of Region-DR solution](https://docs.redhat.com/en/documentation/red_hat_openshift_data_foundation/4.19/html-single/configuring_openshift_data_foundation_disaster_recovery_for_openshift_workloads/index#components-of-regional-dr-solution_rdr).


## OpenShift Native Integration
{: #openshift-native-integration}

When selecting a storage system, your goal should be on identifying a solution that fully integrates with OpenShift APIs and operators, Dynamically provisioning Persistent Volumes (PVs) for container workloads using a CSI driver, lifecycle management, monitoring, and seamless orchestration of containerized workloads. This ensures storage behaves like a native part of the cluster.

## Unified Storage
{: #unified-storage}

While choosing a storage system for your stateful applications, select a single platform that is capable of providing multiple storage types such as block, file, and object, so that different workload requirements can be met without managing multiple underlying storage systems. Unified storage simplifies operations, reduces complexity, and supports diverse enterprise workloads.

## Performance
{: #storage-performance}

To ensure that your application perform optimally under heavy load, select a block storage tier that meets your organization application performance baseline. {{site.data.keyword.Bluemix_notm}} provides various block storage performance tiers with differing performance characteristics, select a tier that meets your applications IOPS and throughput needs. When using VPC clusters with VSIs, for performance considerations, use VPC Block storage 10 IOPS for worker nodes.

For more information refer to [Block Storage profile families](https://cloud.ibm.com/docs/vpc?topic=vpc-block-storage-profiles&interface=ui#block-storage-profile-overview).




## Resources
{: #storage-design-resources}

For more information on storage design considerations, see [{{site.data.keyword.redhat_openshift_notm}} on VPC resiliency](/docs/pattern-openshift-vpc-mz-resiliency?topic=pattern-openshift-vpc-mz-resiliency-overview) and [Understanding OpenShift Data Foundation](/docs/openshift?topic=openshift-ocs-storage-prep).
