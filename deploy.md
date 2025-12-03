---

copyright:
  years: 2025
lastupdated: "2025-12-03"

keywords:

subcollection: pattern-openshift-vpc-dr-multiregion

---

{{site.data.keyword.attribute-definition-list}}


# Deploying
{: #multiregion-openshift-deploying}

## Deploying a Red Hat OpenShift VPC multiregion architecture
This deployment guide outlines steps required to deploy a Red Hat OpenShift Container architecture in a multi region resilient configuration, specifically in three availability zones across two different regions. The deployment is based on an existing deployable architecture template, as well as a series of customizations to tailor the setup to the specific requirements for your environment.

This is designed for customers who need a scalable, multizone and multi region Kubernetes infrastructure with the flexibility of customizations after the initial deployment of the base deployable architecture. It allows for adapting various components, such as networking and security, to better suit individual business needs after the foundational architecture has been established.


## Before you begin
{: #openshift-prereqs}

Before you get started, be sure that you have the following prerequistes:

### Prerequisites
{: #openshift-prereqs}

You need the following items to deploy and configure this reference architecture:

-	An [IBM Cloud account](https://cloud.ibm.com/registration).
-	[Required IAM access policies](https://github.com/terraform-ibm-modules/terraform-ibm-web-app-mzr-da/tree/main/solutions/e2e#required-iam-access-policies).
-	An understanding of the [Planning for the landing zone deployable architectures](/docs/secure-infrastructure-vpc?topic=secure-infrastructure-vpc-plan).

Minimum required permissions
a.	Administrator platform access role
b.	Manager service access role for the cluster in IBM Cloud Kubernetes Service.

In addition, refer to [Deploying OpenShift Data Foundation on VPC clusters](/docs/openshift?topic=openshift-deploy-odf-vpc&interface=ui#ocs-storage-vpc) for complete list of prerequisites.

## Provisioning the architecture
{: #deployment-steps}

The following steps will help you deploy Red Hat OpenShift Cluster and OpenShift Data Foundation clusters across multiple regions and enable asynchronous replication between primary and secondary ODF volumes.

1.	Identify regions where you want to deploy your OpenShift Clusters.
This architecture will require creating three different Red Hat OpenShift Clusters in three different regions. So, you need to first identify which regions you want your clusters to be deployed in.

2.	Create or Select an existing VPC in each region that you want to use as primary, secondary and RHAMC regions. Ensure each subnet where cluster nodes will be deployed, have a public gateway attached to them.
For more information about creating VPC, refer to [VPC multi-zone region](/docs/vpc?topic=vpc-creating-a-vpc-in-a-different-region&interface=cli).

3.	Create the three VPC OpenShift clusters (each in different regions and VPCs) and set the ``--disable-outbound-traffic-protection`` parameter for each. Refer to [Creating Clusters](/docs/openshift?topic=openshift-openshift_odf_rdr_roks&interface=ui#odf-rdr-clusters) for more information.

4.	Enable [Red Hat OpenShift Data Foundation](/docs/openshift?topic=openshift-openshift_odf_rdr_roks&interface=ui#odf-rdr-enable-redhat) on the primary and secondary clusters.

5.	Setup and configure [Red Hat Advanced Cluster Management](/docs/openshift?topic=openshift-openshift_odf_rdr_roks&interface=ui#odf-rdr-install-acm) on the ACM cluster.
With this set up, the ACM cluster manages the ODF clusters. So that if one ODF cluster goes down, then the ACM cluster rolls over the apps and data from that cluster to the other cluster.

6.	Deploy a [sample application](https://docs.redhat.com/documentation/red_hat_openshift_data_foundation/4.16/html-single/configuring_openshift_data_foundation_disaster_recovery_for_openshift_workloads/index#create-sample-application-for-testing-mdrsolution_manage-rdr) to test your cluster and underlying ODF storage system using persistent volume claims

7.	Test the sample [application failover]/docs/openshift?topic=openshift-openshift_odf_rdr_roks&interface=ui#odf-rdr-test) from primary to secondary regions and relocate back to primary region.
