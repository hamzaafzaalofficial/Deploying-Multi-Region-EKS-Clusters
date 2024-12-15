# Deploying-Multi-Region-EKS-Clusters
## Tasks:
Set up EKS clusters in two different AWS regions.
Configure inter-cluster communication.
Deploy an application spanning both clusters.


# Requirements
An AWS account with required permissions to create and manage EKS clusters, EC2 instances, and EBS volumes.
AWS CLI installed and configured.
kubectl installed.
eksctl installed.
IAM Cluster Permission.


# Create the First Cluster
Run the following command to create the first EKS cluster in theus-east-2region:

eksctl create cluster \
  --name=eks-1 \
  --version=1.27 \
  --region=us-east-2 \
  --spot \
  --node-type=t2.medium \
  --nodes=1 \
  --nodes-min=1 \
  --nodes-max=2 \
  --nodegroup-name=eks-node-grp-2 \
  --managed



# Create the Second Cluster
Then, create the second EKS cluster in a different region (us-west-1):

eksctl create cluster \
  --name=eks-2 \
  --version=1.27 \
  --region=us-west-1 \
  --spot \
  --node-type=t2.medium \
  --nodes=1 \
  --nodes-min=1 \
  --nodes-max=2 \
  --nodegroup-name=eks-node-grp-2 \
  --managed \
  --vpc-cidr=10.0.0.0/24

## Task 2: Configure Inter-Cluster Communication


Step 1: Navigate to the VPC Dashboard
Step 2: Initiate VPC Peering
In the VPC Dashboard's left-hand pane, click on "Peering Connections."
Click the "Create Peering Connection" button.
For the "Name tag," give it a descriptive name, e.g., "EKS-inter-cluster-peering."
For "VPC (Requester)", select the VPC of your EKS cluster in us-east-2.
For "Account", select "My Account" (assuming both EKS clusters are in the same AWS account).
For "Region", select "Another region" and then select us-west-1.
For "VPC (Accepter)", select the VPC of your EKS cluster in us-west-1.
Click the "Create Peering Connection" button.

# Step 3: Accept the Peering Request
Switch to the us-west-1 region using the region selector at the top-right corner of the AWS Management Console.
Go back to "Peering Connections" under the VPC Dashboard.
You'll see the peering connection you created; its status will be "Pending Acceptance."
Select the peering connection and click the "Actions" button, then choose "Accept Request."
In the pop-up dialog, click the "Yes, Accept" button.



# Task 3: Deploy an Application Spanning Both Clusters


kubectl config get-context
kubectl config use-context <name of cluster> #you will use this command to switch #when deploying an application in that us-west-1 # #Cluster. 
For demonstration, let's deploy a simple Nginx deployment:

On us-east-2 Cluster
kubectl create deployment nginx --image=nginx


On us-west-1 Cluster
kubectl create deployment nginx --image=nginx

# Verify Deployment
Expose each deployment:
kubectl expose deployment nginx --type=LoadBalancer --port=80

