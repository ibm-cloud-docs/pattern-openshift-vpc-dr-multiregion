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
