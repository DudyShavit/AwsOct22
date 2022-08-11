# Route 53

## Resolving google.com

We will resolve google.com step by step using the `nslookup` command.

1. First, get the list of the root-level DNS servers by `nslookup -type=NS .`.
2. Pick one of the root-level domain names. We will query this server to get the hostname of the *.com* top-level domain by:
   `nslookup -type=NS com <your-chosen-root-level-hostname>`
3. Now that we have a list of *.com* TLD servers, pick on of them to query the hostname of the authoritative DNS of *google.com*:
   `nslookup -type=NS google.com <your-chosen-TLD-hostname>`
4. Finally, as we know the hostname of the authoritative DNS servers of *google.com*, we can query one of them to retrieve the IP address of *google.com*:
   `nslookup -type=A google.com <your-chosen-authoritative-hostname>`

## Creating a public hosted zone

1. Open the Route 53 console at [https://console\.aws\.amazon\.com/route53/](https://console.aws.amazon.com/route53/).

2. If you're new to Route 53, choose **Get started** under **DNS management**\.

   If you're already using Route 53, choose **Hosted zones** in the navigation pane\.

3. Choose **Create hosted zone**\.

4. In the **Create Hosted Zone** pane, enter the name of the domain that you want to route traffic for\.

5. For **Type**, accept the default value of **Public Hosted Zone**\.

6. Choose **Create**\.

7. Inspired by the above usage of `nslookup` try to query one of the authoritative DNS server created in the hosted zone.

## DNS failover during disaster recovery

Regional failover routing is a common pattern for DR events.
In the **simplest** case, we’ve deployed an application in a primary Region and a backup Region (active-active architectures).
We have a Route 53 DNS record set with records for both Regions, and all traffic goes to the primary Region.
In an event that triggers our DR plan, we manually or automatically switch the DNS records to direct all traffic to the backup Region.

### Enable cross-region replication for an S3 bucket

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

2. In the **Buckets** list, choose the name of the bucket that you want to replicate (create a new one if needed)\.

3. Choose **Management**, scroll down to **Replication rules**, and then choose **Create replication rule**\.

4. Under **Rule name**, enter a name for your rule to help identify the rule later\. 

5. Under AWS Identity and Access Management \(IAM\), choose **Create new role**. This is the role that S3 can assume to replicate objects on your behalf\.

6. Under **Status**, see that **Enabled** is selected by default\. An enabled rule starts to work as soon as you save it\. If you want to enable the rule later, select **Disabled**\.

7. If the bucket has existing replication rules, you are instructed to set a priority for the rule\. You must set a priority for the rule to avoid conflicts caused by objects that are included in the scope of more than one rule\. In the case of overlapping rules, Amazon S3 uses the rule priority to determine which rule to apply\. The higher the number, the higher the priority\. For more information about rule priority, see [Replication configuration](replication-add-config.md)\.

8. In the **Replication rule configuration**, under **Source bucket**, choose **This rule applies to all objects in the bucket**\.

9. Under **Destination**, select the bucket where you want S3 to replicate objects (create new if you don't have another available bucket located in different region than the current)\.

10. To finish, choose **Save**\.

11. After you save your rule, you can edit, enable, disable, or delete your rule by selecting your rule and choosing **Edit rule**\.


### Deploy your app in two different regions 

2. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

3. Launch two EC2 instances corresponding to your S3 bucket regions, as follows:
   1. AMI `YoutubeBot`.
   2. Under **Network settings** choose **Allow HTTP traffic from the internet**.
   3. In **Advanced details** choose `youtube-bot-role` as IAM instance profile, and put
      ```shell
      cd AwsJune22/youtubeBot
      echo "BUCKET_NAME=<your-bucket-name>" >> .env
      ```
      In User data, while `<your-bucket-name>` is your bucket name in each region.

### Create Health checks for your app

1. Open the Route 53 console at [https://console\.aws\.amazon\.com/route53/](https://console.aws.amazon.com/route53/)\.
2. In the navigation pane, choose **Health Checks**\.
3. Enter the applicable values for the two EC2 instances, use the machine public IP address as the endpoint\.
4. Choose **Create Health Check**\.

### Create a (sub)domain with failover records 

1. In the Route53 navigation pane, choose **Hosted zones**\.

2. Choose `aws-int-college.click` as the name of the hosted zone that you want to create records in\.

3. Choose **Create record**\.

4. Under **Record name** choose custom subdomain (e.g. `my-name.aws-int-college.click`).
5. Define two failover A records, one of **Primary** type, the other is **Secondary**. The records value is the public IP address the EC2 instance.
6. For each record, choose the corresponding health check you created above.
7. Choose **Create records**\.
   **Note**  
   Your new records take time to propagate to the Route 53 DNS servers

### Simulate a failover

Stop the EC2 instance defined as primary, observe how Route53 is failing over the secondary instance.  
