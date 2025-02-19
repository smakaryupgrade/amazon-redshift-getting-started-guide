# Step 1: Create a sample Amazon Redshift cluster<a name="rs-gsg-launch-sample-cluster"></a>

For any operation that accesses data from another AWS resource, your cluster needs permission to access the resource and the data on the resource on your behalf\. An example is using a COPY command to load data from Amazon Simple Storage Service \(Amazon S3\)\. You provide those permissions by using AWS Identity and Access Management \(IAM\)\. You can do this through an IAM role that is attached to your cluster\. Or you can provide the AWS access key for an IAM user that has the necessary permissions\. For more information about credentials and access permissions, see [Credentials and access permissions](https://docs.aws.amazon.com/redshift/latest/dg/loading-data-access-permissions.html)\. 

To best protect your sensitive data and safeguard your AWS access credentials, we recommend creating an IAM role and attaching it to your cluster\. For more information about providing access permissions, see [Permissions to access other AWS resources](https://docs.aws.amazon.com/redshift/latest/dg/copy-usage_notes-access-permissions.html)\.

In this step, you create a new IAM role that allows Amazon Redshift to load data from Amazon S3 buckets\. An IAM role is an IAM identity that you can create in your account that has specific permissions\. In the next step, you attach the role to your cluster\.

## <a name="rs-gsg-how-to-create-an-iam-role"></a>

**To create an IAM role for Amazon Redshift**

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Roles**\.

1. Choose **Create role**\.

1. In the **AWS Service** group, choose **Redshift**\. 

1. Under **Select your use case**, choose **Redshift \- Customizable**, then choose **Next: Permissions**\.

1. On the **Attach permissions policies** page, choose **AmazonS3ReadOnlyAccess**\. You can keep the default setting for **Set permissions boundary**\. Then choose **Next: Tags**\.

1. The **Add tags** page appears\. You can optionally add tags\. Choose **Next: Review**\.

1. For **Role name**, enter a name for your role\. For this tutorial, enter **myRedshiftRole**\. 

1. Review the information, and then choose **Create Role**\.

1. Choose the role name of the role that you just created\.

1. Copy the **Role ARN** value to your clipboard—this value is the Amazon Resource Name \(ARN\) for the role that you just created\. You use that value when you use the COPY command to load data in [Step 4: Load data from Amazon S3 to Amazon Redshift](rs-gsg-create-sample-db.md)\.

Now that you have created the new role, your next step is to attach it to your cluster\. You can attach the role when you launch a new cluster or you can attach it to an existing cluster\. In the next step, you attach the role to a new cluster\.

The cluster that you are about to create is live, not running in a sandbox\. You incur the standard Amazon Redshift usage fees for the cluster until you delete it\. If you complete the tutorial described here in one sitting and delete the cluster when you are finished, the total charges are minimal\. 

**To create an Amazon Redshift cluster**

1. Sign in to the AWS Management Console and open the Amazon Redshift console at [https://console\.aws\.amazon\.com/redshift/](https://console.aws.amazon.com/redshift/)\.
**Important**  
If you use IAM user credentials, ensure that you have the necessary permissions to perform the cluster operations\. For more information, see [Controlling access to IAM users](https://docs.aws.amazon.com/redshift/latest/mgmt/iam-redshift-user-mgmt.html) in the *Amazon Redshift Cluster Management Guide*\.

1. At upper right, choose the AWS Region where you want to create the cluster\. 

1. On the navigation menu, choose **CLUSTERS**, then choose **Create cluster**\. The **Create cluster** page appears\.

1. In the **Cluster configuration** section, specify values for **Cluster identifier**, **Node type**, and **Nodes**: 
   + **Cluster identifier**: Enter **examplecluster** for this tutorial\. This identifier must be unique\. The identifier must be from 1–63 characters using as valid characters a–z \(lowercase only\) and \- \(hyphen\)\. 
   + Choose one of the following methods to size your cluster:
**Note**  
The following step assumes an AWS Region that supports RA3 node types\. For a list of AWS Regions that support RA3 node types, see [Overview of RA3 node types](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-clusters.html#rs-ra3-node-types) in the *Amazon Redshift Cluster Management Guide*\. 
     + If your AWS Region supports RA3 node types, choose either **Production** or **Free trial** to answer the question **What are you planning to use this cluster for?** 

       If your organization is eligible, you might be able to create a cluster under the Amazon Redshift free trial program\. For information about creating clusters using the free trial program, see [Using a sample dataset](sample-data-load.md)\. To do this, choose **Free trial** to create a configuration with the dc2\.large node type\. For more information about choosing a free trial, see [Amazon Redshift free trial](http://aws.amazon.com/redshift/free-trial/)\. 
     + If you don't know how large to size your cluster, choose **Help me choose**\. Doing this starts a sizing calculator that asks you questions about the size and query characteristics of the data that you plan to store in your data warehouse\. 

       If you know the required size of your cluster \(that is, the node type and number of nodes\), choose **I'll choose**\. Then choose the **Node type** and number of **Nodes** to size your cluster for the proof of concept\.
   + Choose **dc2\.large**for **Node type** and **2** for **Nodes** for this tutorial\.
   + If you have chosen **Production** for your cluster, then do one of the following:
     + To use the sample dataset Amazon Redshift provides, in **Sample data**, choose **Load sample data**\. Amazon Redshift loads the sample dataset Tickit to the default `dev` database and `public` schema\.
     + To bring your own data to Amazon Redshift, continue with the rest of the tutorial\.

1. In the **Database configuration** section, specify values for **Database name \(optional\)**, **Database port \(optional\)**, **Admin user name**, and **Admin user password**\. Or choose **Generate password** to use a password generated by Amazon Redshift\.

   For this tutorial, use these values:
   + **Database name \(optional\)**: Enter **dev**\.
   + **Database port \(optional\)**: Enter **5439**\.
   + **Admin user name**: Enter **awsuser**\.
   + **Admin user password**: Enter a value for the password\.

1. For this tutorial, create an IAM role and set it as the default for your cluster, as described following\. There can only be one default IAM role set for a cluster\. 

   1. Under **Cluster permissions**, for **Manage IAM roles**, choose **Create IAM role**\.

   1. Specify an Amazon S3 bucket for the IAM role to access by one of the following methods:
      + Choose **No additional Amazon S3 bucket** to allow the created IAM role to access only the Amazon S3 buckets that are named as `redshift`\.
      + Choose **Any Amazon S3 bucket** to allow the created IAM role to access all Amazon S3 buckets\. 
      + Choose **Specific Amazon S3 buckets** to specify one or more Amazon S3 buckets for the created IAM role to access\. Then choose one or more Amazon S3 buckets from the table\.

   1. Choose **Create IAM role as default**\. Amazon Redshift automatically creates and sets the IAM role as the default for your cluster\.

      Because you created your IAM role from the console, it has the `AmazonRedshiftAllCommandsFullAccess` policy attached\. This allows Amazon Redshift to copy, load, query, and analyze data from Amazon resources in your IAM account\. 

   For information on how to manage the default IAM role for a cluster, see [Creating an IAM role as default for Amazon Redshift ](https://docs.aws.amazon.com/redshift/latest/mgmt/default-iam-role.html.html) in the *Amazon Redshift Cluster Management Guide*\.

1. \(Optional\) In the **Additional configurations** section, turn off **Use defaults** to modify **Network and security**, **Database configuration**, **Maintenance**, **Monitoring**, and **Backup** settings\.

   In some cases, you might create your cluster with the **Load sample data** option and want to turn on enhanced Amazon VPC routing\. If so, the cluster in your virtual private cloud \(VPC\) requires access to the Amazon S3 endpoint for data to be loaded\. 

   To make the cluster publicly accessible, you can do one of two things\. You can configure a network address translation \(NAT\) address in your VPC for the cluster to access the internet\. Or you can configure an Amazon S3 VPC endpoint in your VPC\. For more information about enhanced Amazon VPC routing, see [Enabling enhanced Amazon VPC routing](https://docs.aws.amazon.com/redshift/latest/mgmt/enhanced-vpc-enabling-cluster.html) in the *Amazon Redshift Cluster Management Guide*\. 

1. Choose **Create cluster**\. 