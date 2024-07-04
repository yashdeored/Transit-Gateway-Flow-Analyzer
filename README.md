# Transit-Gateway-Flow-Analyzer
This is a project where we enable flow logs for an existing Transit Gateway then we publish this logs to S3. We query those logs using athena and visualize it using Amazon Quicksight

In this project:

1. Create three vpcs in the same region with configurations:
   vpc1: 12.0.0.0/16
   vpc2: 13.0.0.0/16
   vpc3: 14.0.0.0/16

2. Subnet association:
   subnet-1: CIDR: 12.0.0.0/24
   subnet-2: CIDR: 13.0.0.0/24
   subnet-3: CIDR: 14.0.0.0/24

3. Create three Internet Gateways and attach it with each vpc individually.
   igw-1 --> for vpc1
   igw-2 --> for vpc2
   igw-3 --> for vpc3
   
5. Create a transit Gateway with the following configurations:
   name-tag: tgw for vpc-1 vpc-2 vpc-3
   description: transit gateway for vpc 1, vpc 2 and vpc 3
   leave the ASN and CIDR and create a transit gateway
   
6. Create three transit gateway attachment for each vpc such as:
   Name tag - optional: tgw-1
   Transit gateway ID: attach the 'tgw for vpc-1 vpc-2 vpc-3' gateway
   attachment type: VPC
   vpc1ttachment: vpc1: 12.0.0.0/16

   Name tag - optional: tgw-2
   Transit gateway ID: attach the 'tgw for vpc-1 vpc-2 vpc-3' gateway
   attachment type: VPC
   vpc1ttachment: vpc1: 13.0.0.0/16

   Name tag - optional: tgw-3
   Transit gateway ID: attach the 'tgw for vpc-1 vpc-2 vpc-3' gateway
   attachment type: VPC
   vpc1ttachment: vpc1: 14.0.0.0/16

   
7. Create a three route tables for three vpcs and update the following for:
   rtb-1(for vpc1):
   0.0.0.0/0 --> Internet Gateway (Attached with vpc1) igw-1
   13.0.0.0/16 --> transit gateway attachment --> tgw-2
   14.0.0.0/16 --> transit gatewat attachment --> tgw-3

   rtb-2(for vpc2):
   0.0.0.0/0 --> Internet Gateway (Attached with vpc2) igw-2
   12.0.0.0/16 --> transit gateway attachment --> tgw-1
   14.0.0.0/16 --> transit gatewat attachment --> tgw-3

   rtb-3(for vpc3):
   0.0.0.0/0 --> Internet Gateway (Attached with vpc3) igw-3
   13.0.0.0/16 --> transit gateway attachment --> tgw-2
   12.0.0.0/16 --> transit gatewat attachment --> tgw-1

8. Create three ec2 instances with configurations as follows:
   Name: instance-1
   AMI: Amazon Linux 2023 AMI
   Instance type: t2.micro
   key-pair login: Create a New Key-pair:
   name: key-1
   key-pair type: RSA
   private key file format: .pem
   Security --> Edit:
   vpc: vpc1
   subnet: subnet-1
   create security group:
   name: sg-1
   description: security configurstion for instance.
   Inbound secrity rule:
   ssh --> anywhere --> 0.0.0.0/0
   http --> anywhere --> 0.0.0.0/0

   user data script:
   ```
   #!/bin/bash
   yes | sudo apt update
   yes | sudo apt install apache2
   echo "<h1>Server Details</h1><p><strong>Hostname:</strong> $(hostname)</p><p><strong>IP Address:</strong> $(hostname -I | cut -d" " -f1)</p>" > /var/www/html/index.html
   sudo systemctl restart apache2
   ```

   Name: instance-2
   AMI: Amazon Linux 2023 AMI
   Instance type: t2.micro
   key-pair login: Create a New Key-pair:
   name: key-2
   key-pair type: RSA
   private key file format: .pem
   Security --> Edit:
   vpc: vpc2
   subnet: subnet-2
   create security group:
   name: sg-2
   description: security configurstion for instance.
   Inbound secrity rule:
   ssh --> anywhere --> 0.0.0.0/0
   http --> anywhere --> 0.0.0.0/0

   user data script:
   ```
   #!/bin/bash
   yes | sudo apt update
   yes | sudo apt install apache2
   echo "<h1>Server Details</h1><p><strong>Hostname:</strong> $(hostname)</p><p><strong>IP Address:</strong> $(hostname -I | cut -d" " -f1)</p>" > /var/www/html/index.html
   sudo systemctl restart apache2
   ```

   Name: instance-3
   AMI: Amazon Linux 2023 AMI
   Instance type: t2.micro
   key-pair login: Create a New Key-pair:
   name: key-1
   key-pair type: RSA
   private key file format: .pem
   Security --> Edit:
   vpc: vpc3
   subnet: subnet-3
   create security group:
   name: sg-3
   description: security configurstion for instance.
   Inbound secrity rule:
   ssh --> anywhere --> 0.0.0.0/0
   http --> anywhere --> 0.0.0.0/0

   user data script:
   ```
   #!/bin/bash
   yes | sudo apt update
   yes | sudo apt install apache2
   echo "<h1>Server Details</h1><p><strong>Hostname:</strong> $(hostname)</p><p><strong>IP Address:</strong> $(hostname -I | cut -d" " -f1)</p>" > /var/www/html/index.html
   sudo systemctl restart apache2
   ```

Connect instance-1 by selecting it and go to SSH client where the code to ssh is given and open the  terminal through cloud9 or you can also open just the  terminal or on your local network by vs code.

commands to run after running the code from SSH client:
for vpc1:
```
curl (private ip of vpc2)
curl (private ip of vpc3)
```

for vpc2:
```
curl (private ip of vpc2)
curl (private ip of vpc1)
```

for vpc3:
```
curl (private ip of vpc1)
curl (private ip of vpc3)
```


1. Enable Flow Logs on an existing Transit Gateway

Navigate to the Amazon VPC Dashboard 

Select Transit Gateways from the left-hand menu and choose the transit gateway created from the CloudFormation Template.

Click on the Flow logs tab and then click on Create flow log to enable flow log on the selected Transit Gateway.

Provide a name tag and select the Destination as Send to an Amazon S3 bucket. Specify the S3 bucket ARN (obtained from the Outputs section of the CloudFormation stack corresponding to Key TGWFlowLogsS3BucketARN).

Click on the slider corresponding to Turn on under Hive-compatible S3 prefix. Next, under Log file format select Parquet. Rest all parameters can be left to its default values.

Click on Create flow log to enable the flow logs.

Now flow logs have been enabled for Transit Gateway and records will be stored in the specified Amazon S3 bucket.

2. Publish those logs to S3 bucket

Connect to the Amazon EC2 Instance.

Click on the EC2 instance with Name as vpc1 - EC2 Instance and note the IP address listed under Private IPv4 addresses.

Click on the EC2 instance with Name as vpc2 - EC2 Instance and click on Connect. Choose Session Manager tab and click on Connect button.

Initiate traffic to IPv4 public endpoint(s) on Internet. For example, use:
```
curl 'https://api.ipify.org?format=json'
```

Expected Response: {"ip":"Your IPv4 address"}

Initiate ping to EC2 instance in vpc1 (Use IP address obtained from Step 2).
```
ping 10.0.0.240
```
Expected Response: Successful response to ping requests.

(Optional) You can choose to initiate traffic from vpc1 - EC2 Instance as well by repeating the steps 3-5.
Verify Flow Logs are being published to Amazon S3 bucket (Optional)
Navigate to Amazon VPC dashboard .

Select the Transit Gateway created by CloudFormation stack and click on Flow logs tab.

Click on S3 bucket to which flow logs are being sent to, noted under Destination name. This would open Amazon S3 dashboard in a new tab.

Navigate to the relevant folder and you can verify log files being present (You may need to wait for few minutes to see the relevant files being populated in Amazon S3 bucket).


4. Run Athena queries on Flow Log records

Glue and Athena Setup
We will leverage pre-defined Amazon Athena  queries, created by  CloudFormation template in following steps, for answering commonly used questions related to understanding network traffic patterns.

Deploy the CloudFormation Template
For the purpose of this workshop, we will use a pre-created CloudFormation template that creates:

<a href="https://ws-assets-prod-iad-r-iad-ed304a55c2ca1aee.s3.us-east-1.amazon.com/5ea3c65f-555c-45c2-b878-4f1a45efb7e2/tgw-flow-athena-setup.yml">Template</a>

A partitioned table in  Glue  corresponding to the TGW Flow Logs records.
A database in  Glue to store the Glue tables.
An  Lambda  function that loads new partitions to the table daily.
An  Identity and Access Management (IAM) role  that grants permission to run the Lambda function.
A workgroup in Athena to store the named queries, along with a set of named queries in the workgroup.
Download the CloudFormation template to your local machine.

Navigate to Cloudformation console  and click Create Stack > With new resources (standard).

For Prepare template select Template is ready, and select Upload a template file under Template source. Choose the CloudFormation template that you downloaded at the begining of this section Click and click Next.

Provide a Stack name. Then, provide the S3 Buckets ARN and URI path inputs (obtained from the Outputs section of the first CloudFormation Stack) in the format specified. No changes are necessary for other parameters. Once done, click Next.

Click Next till you get to the Review page. Check the box for I acknowledge that  CloudFormation might create IAM resources with custom names and click Submit.

Wait for the stack creation to reach CREATE_COMPLETE.

Verify Athena Table
Now that the CloudFormation template is successfully created, let's validate Athena table is created and we are able to retrieve flow log records from S3 bucket.

Navigate to the Athena Dashboard  from the console.

Click on Query editor from the left-hand navigation panel. On top right side, choose TGWFlowLogsQueryWorkGroup from Workgroup drop down menu. Click on Acknowledge button when prompted to confirm the query result settings.

Back on Query editor page, click on Editor tab. Ensure DataCatalog is selected under Data source and tgwflowlogsathenadatabase is selected under Database. Under Tables you would see an existing table named tgw_flow_logs. Click on the expand button and then click on Load partitions button. Once, you get a success message, click on the expand button corresponding to tgw_flow_logs table and click on Preview Table. This would execute the default query on the table and you should see flow log entries under the Results tab.

(Optional) Click on Saved queries tab and run any one (or more) of the named queries created by the aforementioned CloudFormation template.

6. Visualize using Amazon QuickSight

QuickSight Setup
Sign up for Amazon QuickSight subscription
If you are running this workshop in your own  account, and you have used Amazon QuickSight service in the past, you can log in using your account. If you are at an  hosted event with temporary accounts provided, or have never used Amazon QuickSight service in your own  account in the past, you have to sign up for Amazon QuickSight by following the below steps.

Navigate to the Amazon QuickSight Dashboard  from the console.


You will be asked to create your QuickSight account. You can leave this to default Enterprise tier and click on Continue button on the bottom of the page. On next page, in the Get Paginated Report add-on pane, click on No, Maybe Later.


On sign up page, Under QuickSight region ensure you have selected the  region in which you are running this workshop. Provide an unique QuickSight account name and your email address. Furthermore, ensure you provide QuickSight access to your Amazon S3 buckets, IAM and Amazon Athena by clicking on respective check boxes.




Click on Finish button to complete creation of your QuickSight account.
You should see a message that your QuickSight account is being created. This will take a few minutes.
In the QuickSight window, click Go to Amazon QuickSight.

Click on the user icon on top right and note down the Username.



Now that Athena queries are setup and we are able to view the results, let's setup Amazon QuickSight  dashboard, using a CloudFormation template as described in following steps, to visualize network traffic.

Deploy the CloudFormation Template
For the purpose of this workshop, we will use a pre-created CloudFormation template that creates a QuickSight Athena Datasource, Datset and Dashboard for Transit Gateway Flow Logs.

Download the CloudFormation template to your local machine.

Navigate to Cloudformation console  and click Create Stack > With new resources (standard).

For Prepare template select Template is ready, and select Upload a template file under Template source. Choose the CloudFormation template that you downloaded at the begining of this section and click Next.
 
Provide a Stack name. Then, under the Parameters section, provide the QuickSight User ARN, for example: arn::quicksight:us-west-2:<account_id>:user/default/WSParticipantRole/Participant and enter tgwflowlogsathenadatabase as input for field TGWFlowLogsAthenaDatabaseName. Once done, click Next.
 
Click Next till you get to the Review page and click on Submit.

Wait for the stack creation to reach CREATE_COMPLETE

Visualise the Dashboard
Navigate to the Amazon QuickSight Dashboard  from the console and login with your QuickSight credentials.

Click on the User icon on top right corner, and ensure you are in the same  region in which you are running this workshop.

From the left navigation panel, click on Dashboards and then click on the dashboard named Transit Gateway Flow Logs Analysis Dashboard.


You will see datapoints for various tables that allows you to visualize network traffic pattern in your  enviornment.


The QuickSight dashboard created by CloudFormation template leverages pre-defined Athena named queries to answer some of the commonly asked questions related to Top Talkers, Traffic volume and so on. Feel free to spend some time to understand and navigate through the QuickSight Dashboard. You can also choose to customize the CloudFormation templates, used in this workshop, to modify the infrastructure setup or queries as per your requirements.

Architecture of the project: 

![Transit Gateway Flow Analyzer](https://github.com/yashdeored/Transit-Gateway-Flow-Analyzer/assets/152061059/53f39e2e-b4c6-4480-ae04-5c3088e7a61d)
