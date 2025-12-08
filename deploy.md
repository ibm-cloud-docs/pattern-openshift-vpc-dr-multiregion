---

copyright:
years: 2025
lastupdated: "2025-12-08"

keywords:

subcollection: pattern-openshift-vpc-dr-multiregion

---

{{site.data.keyword.attribute-definition-list}}


# Deploying a {{site.data.keyword.openshiftlong_notm}} multiregion architecture
{: #Deployment-Guide}

This deployment guide outlines steps required to deploy a Red Hat OpenShift Container architecture in a multiregion resilient configuration, specifically in three availability zones across two different regions. The deployment is based on an existing deployable architecture template, as well as a series of customizations to tailor the setup to the specific requirements for your environment.

This is designed for customers who need a scalable, multizone and multiregion Kubernetes infrastructure with the flexibility of customizations after the initial deployment of the base deployable architecture. It allows for adapting various components, such as networking and security, to better suit individual business needs after the foundational architecture has been established.


## Before you begin
{: #openshift-prereqs}

Before you get started, be sure that you have the following prerequistes:

### Prerequisites
{: #openshift-prereqs}

You need the following items to deploy and configure this reference architecture:

-	An [{{site.data.keyword.Bluemix_notm}} account](https://cloud.ibm.com/registration).

-	[Required IAM access policies](https://github.com/terraform-ibm-modules/terraform-ibm-web-app-mzr-da/tree/main/solutions/e2e#required-iam-access-policies).

-	An understanding of the [Planning for the landing zone deployable architectures](/docs/secure-infrastructure-vpc?topic=secure-infrastructure-vpc-plan).


### Red Hat OpenShift Subscriptions
{: #roks-subscription}

Disaster Recovery features supported by Red Hat OpenShift Data Foundation require all of the following prerequisites to successfully implement a disaster recovery solution:

- A valid Red Hat OpenShift Data Foundation Advanced entitlement

- A valid Red Hat Advanced Cluster Management for Kubernetes subscription

Any Red Hat OpenShift Data Foundation Cluster containing PVs participating in active replication either as a source or destination requires OpenShift Data Foundation Advanced entitlement. This subscription should be active on both source and destination clusters.

To know how subscriptions for OpenShift Data Foundation work, see knowledgebase article on [OpenShift Data Foundation subscriptions](https://access.redhat.com/articles/6932811).


### Minimum required permissions
{: #min-permissions}

1. Administrator platform access role.  

2.	Manager service access role for the cluster in IBM Cloud Kubernetes Service.  

3. In addition, refer to [Deploying OpenShift Data Foundation on VPC clusters](/docs/openshift?topic=openshift-deploy-odf-vpc&interface=ui#ocs-storage-vpc) for complete list of prerequisites.  




### Provisioning the architecture
{: #deployment-steps}

The following steps will help you deploy Red Hat OpenShift Cluster and OpenShift Data Foundation clusters across multiple regions and enable asynchronous replication between primary and secondary ODF volumes. 



1. Identify regions where you want to deploy your OpenShift Clusters. This architecture will require creating three different Red Hat OpenShift Clusters in three different regions. So, you need to first identify which regions you want your clusters to be deployed in.  



2.	Create or Select an existing VPC in each region that you want to use as primary, secondary and RHAMC regions. Ensure each subnet where cluster nodes will be deployed, have a public gateway attached to them. For more information about creating VPC, refer to [VPC multi-zone region](/docs/vpc?topic=vpc-creating-a-vpc-in-a-different-region&interface=cli).  



3.	Create the three VPC OpenShift clusters (each in different regions and VPCs) and set the **--disable-outbound-traffic-protection** parameter for each. Refer to [Creating Clusters](/docs/openshift?topic=openshift-openshift_odf_rdr_roks&interface=ui#odf-rdr-clusters) for more information.  
The commands in the referrenced link will guide you to create an OpenShift cluster in a single zone which will provide 99.9% availability, if you need 99.99% availability in each region then add worker nodes to the worker pool in additonl zones in each region. For more information refer to [Adding worker nodes to VPC clusters](https://cloud.ibm.com/docs/openshift?topic=openshift-add-workers-vpc) on how to resize workerpool in an existing OpenShift cluster.


   **Note:** The ***ibmcloud ks cluster*** command in the reference link points to OpenShift version 4.17.10, please change this to your preferred version of OpenShift, it is recommended to always use the latest version of OpenShift.



4.	Enable [Red Hat OpenShift Data Foundation](/docs/openshift?topic=openshift-openshift_odf_rdr_roks&interface=ui#odf-rdr-enable-redhat) on the primary and secondary clusters.  


   **Note:** The Operation Hub might be enabled by default on some latest versions of the Red Hat OpenShift, so this step is optional depending on your cluster version.  



5.	Setup and configure [Red Hat Advanced Cluster Management](/docs/openshift?topic=openshift-openshift_odf_rdr_roks&interface=ui#odf-rdr-install-acm) on the ACM cluster.  



With this set up, the ACM cluster manages the ODF clusters. So that if one ODF cluster goes down, then the ACM cluster rolls over the apps and data from that cluster to the other cluster.  

   **Note**: Some configuration links referring to Red Hat documentation might lead you to older versions of OpenShift, please ensure to change the version number from the drop-down on the left side of the document.  



6.	Install [OpenShift Data Foundation](/docs/openshift?topic=openshift-deploy-odf-vpc&interface=ui#install-odf-console-vpc) on the two managed clusters and ensure the two important options, Enable DR and NooBaa, are selected.  



**Use these parameter values**.  

- OSD Storage Class Name: Provide a name of your choice and select a storage class with the VolumeBindingMode of WaitForFirstConsumer.  
- Pod Size: Enter 512Gi.  
- Worker Nodes: Leave it blank to install ODF on all nodes in the cluster.  
- Number of OSD Disks Required: Start with just 1 on each node for this test.  
- Enable Cluster Encrytpion and Enable Volume Encryption are option, so leave the disabled.  

Complete the above steps on both primary and secondary managed OpenShift clusters.
Installation will complete in few minutes, after that use the below OC command to validate the installation is successful on both managed clusters.  

      oc get storagecluster -n openshift-storage ocs-storagecluster -o jsonpath='{.status.phase}{"\n"}

If the output of the above command is **Ready**, then move to next step.  



7. As ``GlobalNet`` was enabled during the Submariner add-on installation, update the **ACM Managed Cluster Name** in the storageclusterresource’s multiClusterService section for ODF to use GlobalNet.  

Run the below command to update the managed cluster names in the storage cluster.  

      kubectl patch storagecluster -n openshift-storage ocs-storagecluster --type merge -p'{"spec":{"network":{"multiClusterService":{"clusterID":"managed-cluster-1-dr-odf","enabled":true}}}}’. 

   **Note**: Ensure to run this command on both managed clusters and add the respective cluster names in storage cluster.  

After the changes, run the following command to validate the pods have restarted.  

      oc get serviceexport -n openshift-storage

Ensure the output looks like this.  

      NAME              AGE
      rook-ceph-mon-d   14d
      rook-ceph-mon-e   14d
      rook-ceph-mon-f   14d
      rook-ceph-osd-0   14d
      rook-ceph-osd-1   14d
      rook-ceph-osd-2   14d


8. Install ODF Multicluster Orchestrator to the ACM hub cluster. For more information, see section 4.5 at Installing ODF Multicluster Orchestrator on Hub cluster.  


   **Note:** Section 4.6 is optional and does not have an impact on this guide. For security reasons you must enable and configure SSL across clusters.     


11. Create a DR Policy from the Hub cluster  with a sync interval of 5 minutes. For more information, see section 4.7 at Creating Disaster Recovery Policy on Hub cluster.  


   **Note:** The synch interval defines the RPO of your application. Choose a sync interval that meets your organization or application acceptable data loss requirement.  


### Verifying the architecture. 
{: #verify-deployment}


1. Deploy a subscription based [sample application](https://docs.redhat.com/en/documentation/red_hat_openshift_data_foundation/4.19/html-single/configuring_openshift_data_foundation_disaster_recovery_for_openshift_workloads/index#create-sample-application-for-testing-mdrsolution_manage-rdr) to test your cluster and underlying ODF storage system using persistent volume claims.  



- While deplpying the application you will need to select the deployment path, choose either RBD or CepfFS.  

- After the application deploys successfully, run the following command to validate.  

      oc get pods,pvc -n busybox-sample.  


2. Create a Disaster Recovery Policy and enroll the sample application in the policy. For more information and detailed steps refer to [Apply Data policy to sample application](https://docs.redhat.com/en/documentation/red_hat_openshift_data_foundation/4.19/html-single/configuring_openshift_data_foundation_disaster_recovery_for_openshift_workloads/index#apply-drpolicy-to-sample-application_manage-rdr).  



After you assign a DR policy and enroll your application, **DR Status** shows either **healthy** or **critical** when failover or relocate is not taking place. So, wait for few minutes for the appropriate status to show.  



3. Test the sample can [application failover](/docs/openshift?topic=openshift-openshift_odf_rdr_roks&interface=ui#odf-rdr-test) from primary to secondary region and then relocate back to primary region.  

- Verify that the application pods are running on the primary cluster.
Run the oc get pods -n busybox-sample command on primary cluster to ensure the busy-box application pods are running.  

The output should look similar to the one below.  

      NAME                      READY   STATUS    RESTARTS   AGE
      busybox-6bb69c6ff-79bkr   1/1     Running   0          14d

Run the ``oc get pods -n busybox-sample`` on the secondary cluster and the output should look similar to the one below.  

   No resources found in busybox-sample namespace.  

- Initiate application failover from primary to secondary, For more information refer to [Subscription-based application failover between managed clusters](https://docs.redhat.com/en/documentation/red_hat_openshift_data_foundation/4.19/html-single/configuring_openshift_data_foundation_disaster_recovery_for_openshift_workloads/index#application-failover-between-managed-clusters_manage-rdr).  

**Note:** ``LastGroupSyncTime`` is a critical metric that reflects the time since the last successful replication occurred for all PVCs associated with an application. In essence, it measures the synchronization health between the primary and secondary clusters. So, prior to initiating a failover from one cluster to another, check for this metric and only initiate the failover if the ``LastGroupSyncTime`` is within a reasonable time in the past.  



4. Once you have validated that your application has been failed over to the secondary region, let us now try to relocate it back to the primary region. For more information, refer to [Relocating Subscription-based application between managed clusters](https://docs.redhat.com/en/documentation/red_hat_openshift_data_foundation/4.19/html-single/configuring_openshift_data_foundation_disaster_recovery_for_openshift_workloads/index#relocating-application-between-managed-clusters_manage-rdr).  
