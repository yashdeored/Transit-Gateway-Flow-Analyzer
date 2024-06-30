<h1>First we need to create a vpc for this architecture we will be creating 3 vpcs in the same region</h1>

<h2>Creation of VPC-1: </h2>

Go to <a href="https://us-east-1.console.aws.amazon.com/vpcconsole/home?region=us-east-1#vpcs:">VPC Dashboard</a>

![image](https://github.com/yashdeored/Transit-Gateway-Flow-Analyzer/assets/152061059/eace86e4-44a1-44f4-ab6c-2b72de498016)

Go to Create Vpc where you will create your first VPC:

![image](https://github.com/yashdeored/Transit-Gateway-Flow-Analyzer/assets/152061059/29586baf-8ce0-4fa2-95d6-2c27a1b077e4)

Name tag - optional
Creates a tag with a key of 'Name' and a value that you specify.
vpc-1
IPv4 CIDR block
Info
IPv4 CIDR manual input
IPAM-allocated IPv4 CIDR block
IPv4 CIDR
12.0.0.0/24
CIDR block size must be between /16 and /28.
IPv6 CIDR block
Info
No IPv6 CIDR block
IPAM-allocated IPv6 CIDR block
Amazon-provided IPv6 CIDR block
IPv6 CIDR owned by me
Tenancy Info: Default

Hit the create vpc

for second vpc just add

Name tag - optional: vpc-2
IPv4 CIDR: 13.0.0.0/24

for third vpc just add:

Name tag - optional: vpc-3
IPv4 CIDR: 14.0.0.0/24

![image](https://github.com/yashdeored/Transit-Gateway-Flow-Analyzer/assets/152061059/60ef9480-6c65-49b5-b432-441581357fec)

For the subnet configuration add the same CIDR for the subnet for all the 3 vpcs and create 3 subnets

![image](https://github.com/yashdeored/Transit-Gateway-Flow-Analyzer/assets/152061059/341e525c-98fd-4c2b-8e53-fa9f6cd0c1a3)

it should look like this:

<img width="1440" alt="Screenshot 2024-06-30 at 7 09 21 PM" src="https://github.com/yashdeored/Transit-Gateway-Flow-Analyzer/assets/152061059/664f20bb-a701-44a8-92f9-3db2796415bd">

Now update the route table for each of the 3 vpc:

<img width="1440" alt="Screenshot 2024-06-30 at 7 10 04 PM" src="https://github.com/yashdeored/Transit-Gateway-Flow-Analyzer/assets/152061059/51dfe0c6-da61-48f7-b612-8f36d24abe7b">

It should be as follows:

<img width="1440" alt="Screenshot 2024-06-30 at 7 11 02 PM" src="https://github.com/yashdeored/Tran
sit-Gateway-Flow-Analyzer/assets/152061059/39161d2c-1476-4528-8f66-2a46afb5bb5f">

Now create a transit gateway:

<img width="1440" alt="Screenshot 2024-06-30 at 7 12 20 PM" src="https://github.com/yashdeored/Transit-Gateway-Flow-Analyzer/assets/152061059/99271485-6c41-4b10-828d-2e73b3068705">

