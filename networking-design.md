---

copyright:
  years: 2025
lastupdated: "2025-12-04"

subcollection: pattern-openshift-vpc-dr-multiregion

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Networking design
{: #NetworkingDesignConsideration}

Before you create your {{site.data.keyword.openshiftlong_notm}} cluster, you must choose a networking setup so that certain cluster components can communicate with each other and with networks or services outside of the cluster.

- Worker-to-worker communication: All worker nodes must be able to communicate with each other on the private network through {{site.data.keyword.vpc_short}} subnets.
- Worker-to-master and user-to-master communication: Your worker nodes and your authorized cluster users can communicate with the Kubernetes master securely over virtual private endpoints or cloud service endpoints.
- Worker communication to other services or networks: Allow your worker nodes to securely communicate with other {{site.data.keyword.Bluemix_notm}} services, such as {{site.data.keyword.registrylong_notm}}, to on-premises networks, to other VPCs, or to classic infrastructure resources.
- External communication to apps that run on worker nodes: Allow public or private requests into the cluster as well as requests out of the cluster to a public endpoint.

When you create {{site.data.keyword.vpc_short}} subnets for your clusters, keep in mind the following features and limitations. For more information about {{site.data.keyword.vpc_short}} subnets, see [Characteristics of subnets in the VPC](https://cloud.ibm.com/docs/vpc?topic=vpc-about-networking-for-vpc#subnets-in-the-vpc).

-	The default CIDR size of each {{site.data.keyword.vpc_short}} subnet is /24, which can support up to 253 worker nodes. If you plan to deploy more than 250 worker nodes per zone in one cluster, consider creating a subnet of a larger size.

-	After you create an {{site.data.keyword.vpc_short}} subnet, you can't resize it or change its IP range.

-	Multiple clusters in the same {{site.data.keyword.vpc_short}} can share subnets.

-	{{site.data.keyword.vpc_short}} subnets are bound to a single zone region and can't span multiple zones or regions.

-	After you create a subnet, you can't move it to a different zone, region, or VPC.

-	Deploy your {{site.data.keyword.openshiftlong_notm}} cluster within an {{site.data.keyword.vpc_short}} provisioned across multiple availability zones within each DR region to provide workload isolation within the public cloud.

- Place each worker node in a separate subnet in each availability zone. Use security groups and access control lists (ACLs) as firewalls to limit access to server instances for operational purposes and to control network traffic.

- Use {{site.data.keyword.alb_full}} to distribute incoming requests to stateful containerized applications.

-	If you have worker nodes that are attached to an existing subnet in a zone, you can't change the subnet for that zone in the cluster.

-	The 172.16.0.0/16, 172.18.0.0/16, 172.19.0.0/16, and 172.20.0.0/16 ranges are prohibited.
