---

copyright:
  years: 2025
lastupdated: "2025-12-03"

subcollection: pattern-openshift-vpc-dr-multiregion

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Networking design
{: #NetworkingDesignConsideration}

The multiregion Disaster Recovery pattern for Red Hat OpenShift using OpenShift Data Foundation uses IBM Cloud VPC infrastructure and network services to segment the management and application workloads and support the application deployment across multiple availability zones in each participating DR region.

•	Deploy the Red Hat OpenShift within a Virtual Private Cloud (VPC) provisioned across multiple availability zones within each DR region to provide workload isolation within the public cloud.

•	Place each worker node in a separate subnet in each availability zone. Use security groups and access control lists (ACLs) as firewalls to limit access to server instances for operational purposes and to control network traffic.

•	Use VPC Application Load Balancers (ALB) to distribute incoming requests to stateful containerized applications.
