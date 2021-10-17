# Kommander Deployment in Business Network

DOCUMENT PUNCH LIST
* Insert Download Link For Kubernete
* Insert Kommander Registry to White List

## Prerequisites

### AWS Permissions
To apply these you can use [clusterawsadm](https://github.com/kubernetes-sigs/cluster-api-provider-aws/releases/tag/v0.6.5 "See our documentation https://docs.d2iq.com/dkp/konvoy/2.0/choose_infrastructure/aws/iam-policies/#iam-artifacts") or paste the stack above into the AWS CloudFormation Templates, or use the AWS console.
```
AWSTemplateFormatVersion: 2010-09-09
Resources:
  AWSIAMInstanceProfileControlPlane:
    Properties:
      InstanceProfileName: control-plane.cluster-api-provider-aws.sigs.k8s.io
      Roles:
      - Ref: AWSIAMRoleControlPlane
    Type: AWS::IAM::InstanceProfile
  AWSIAMInstanceProfileNodes:
    Properties:
      InstanceProfileName: nodes.cluster-api-provider-aws.sigs.k8s.io
      Roles:
      - Ref: AWSIAMRoleNodes
    Type: AWS::IAM::InstanceProfile
  AWSIAMManagedPolicyCloudProviderControlPlane:
    Properties:
      Description: For the Kubernetes Cloud Provider AWS Control Plane
      ManagedPolicyName: control-plane.cluster-api-provider-aws.sigs.k8s.io
      PolicyDocument:
        Statement:
        - Action:
          - autoscaling:DescribeAutoScalingGroups
          - autoscaling:DescribeLaunchConfigurations
          - autoscaling:DescribeTags
          - ec2:DescribeInstances
          - ec2:DescribeImages
          - ec2:DescribeRegions
          - ec2:DescribeRouteTables
          - ec2:DescribeSecurityGroups
          - ec2:DescribeSubnets
          - ec2:DescribeVolumes
          - ec2:CreateSecurityGroup
          - ec2:CreateTags
          - ec2:CreateVolume
          - ec2:ModifyInstanceAttribute
          - ec2:ModifyVolume
          - ec2:AttachVolume
          - ec2:AuthorizeSecurityGroupIngress
          - ec2:CreateRoute
          - ec2:DeleteRoute
          - ec2:DeleteSecurityGroup
          - ec2:DeleteVolume
          - ec2:DetachVolume
          - ec2:RevokeSecurityGroupIngress
          - ec2:DescribeVpcs
          - elasticloadbalancing:AddTags
          - elasticloadbalancing:AttachLoadBalancerToSubnets
          - elasticloadbalancing:ApplySecurityGroupsToLoadBalancer
          - elasticloadbalancing:CreateLoadBalancer
          - elasticloadbalancing:CreateLoadBalancerPolicy
          - elasticloadbalancing:CreateLoadBalancerListeners
          - elasticloadbalancing:ConfigureHealthCheck
          - elasticloadbalancing:DeleteLoadBalancer
          - elasticloadbalancing:DeleteLoadBalancerListeners
          - elasticloadbalancing:DescribeLoadBalancers
          - elasticloadbalancing:DescribeLoadBalancerAttributes
          - elasticloadbalancing:DetachLoadBalancerFromSubnets
          - elasticloadbalancing:DeregisterInstancesFromLoadBalancer
          - elasticloadbalancing:ModifyLoadBalancerAttributes
          - elasticloadbalancing:RegisterInstancesWithLoadBalancer
          - elasticloadbalancing:SetLoadBalancerPoliciesForBackendServer
          - elasticloadbalancing:AddTags
          - elasticloadbalancing:CreateListener
          - elasticloadbalancing:CreateTargetGroup
          - elasticloadbalancing:DeleteListener
          - elasticloadbalancing:DeleteTargetGroup
          - elasticloadbalancing:DescribeListeners
          - elasticloadbalancing:DescribeLoadBalancerPolicies
          - elasticloadbalancing:DescribeTargetGroups
          - elasticloadbalancing:DescribeTargetHealth
          - elasticloadbalancing:ModifyListener
          - elasticloadbalancing:ModifyTargetGroup
          - elasticloadbalancing:RegisterTargets
          - elasticloadbalancing:SetLoadBalancerPoliciesOfListener
          - iam:CreateServiceLinkedRole
          - kms:DescribeKey
          Effect: Allow
          Resource:
          - '*'
        Version: 2012-10-17
      Roles:
      - Ref: AWSIAMRoleControlPlane
    Type: AWS::IAM::ManagedPolicy
  AWSIAMManagedPolicyCloudProviderNodes:
    Properties:
      Description: For the Kubernetes Cloud Provider AWS nodes
      ManagedPolicyName: nodes.cluster-api-provider-aws.sigs.k8s.io
      PolicyDocument:
        Statement:
        - Action:
          - ec2:DescribeInstances
          - ec2:DescribeRegions
          - ecr:GetAuthorizationToken
          - ecr:BatchCheckLayerAvailability
          - ecr:GetDownloadUrlForLayer
          - ecr:GetRepositoryPolicy
          - ecr:DescribeRepositories
          - ecr:ListImages
          - ecr:BatchGetImage
          Effect: Allow
          Resource:
          - '*'
        - Action:
          - secretsmanager:DeleteSecret
          - secretsmanager:GetSecretValue
          Effect: Allow
          Resource:
          - arn:*:secretsmanager:*:*:secret:aws.cluster.x-k8s.io/*
        - Action:
          - ssm:UpdateInstanceInformation
          - ssmmessages:CreateControlChannel
          - ssmmessages:CreateDataChannel
          - ssmmessages:OpenControlChannel
          - ssmmessages:OpenDataChannel
          - s3:GetEncryptionConfiguration
          Effect: Allow
          Resource:
          - '*'
        Version: 2012-10-17
      Roles:
      - Ref: AWSIAMRoleControlPlane
      - Ref: AWSIAMRoleNodes
    Type: AWS::IAM::ManagedPolicy
  AWSIAMManagedPolicyControllers:
    Properties:
      Description: For the Kubernetes Cluster API Provider AWS Controllers
      ManagedPolicyName: controllers.cluster-api-provider-aws.sigs.k8s.io
      PolicyDocument:
        Statement:
        - Action:
          - ec2:AllocateAddress
          - ec2:AssociateRouteTable
          - ec2:AttachInternetGateway
          - ec2:AuthorizeSecurityGroupIngress
          - ec2:CreateInternetGateway
          - ec2:CreateNatGateway
          - ec2:CreateRoute
          - ec2:CreateRouteTable
          - ec2:CreateSecurityGroup
          - ec2:CreateSubnet
          - ec2:CreateTags
          - ec2:CreateVpc
          - ec2:ModifyVpcAttribute
          - ec2:DeleteInternetGateway
          - ec2:DeleteNatGateway
          - ec2:DeleteRouteTable
          - ec2:DeleteSecurityGroup
          - ec2:DeleteSubnet
          - ec2:DeleteTags
          - ec2:DeleteVpc
          - ec2:DescribeAccountAttributes
          - ec2:DescribeAddresses
          - ec2:DescribeAvailabilityZones
          - ec2:DescribeInstances
          - ec2:DescribeInternetGateways
          - ec2:DescribeImages
          - ec2:DescribeNatGateways
          - ec2:DescribeNetworkInterfaces
          - ec2:DescribeNetworkInterfaceAttribute
          - ec2:DescribeRouteTables
          - ec2:DescribeSecurityGroups
          - ec2:DescribeSubnets
          - ec2:DescribeVpcs
          - ec2:DescribeVpcAttribute
          - ec2:DescribeVolumes
          - ec2:DetachInternetGateway
          - ec2:DisassociateRouteTable
          - ec2:DisassociateAddress
          - ec2:ModifyInstanceAttribute
          - ec2:ModifyNetworkInterfaceAttribute
          - ec2:ModifySubnetAttribute
          - ec2:ReleaseAddress
          - ec2:RevokeSecurityGroupIngress
          - ec2:RunInstances
          - ec2:TerminateInstances
          - tag:GetResources
          - elasticloadbalancing:AddTags
          - elasticloadbalancing:CreateLoadBalancer
          - elasticloadbalancing:ConfigureHealthCheck
          - elasticloadbalancing:DeleteLoadBalancer
          - elasticloadbalancing:DescribeLoadBalancers
          - elasticloadbalancing:DescribeLoadBalancerAttributes
          - elasticloadbalancing:ApplySecurityGroupsToLoadBalancer
          - elasticloadbalancing:DescribeTags
          - elasticloadbalancing:ModifyLoadBalancerAttributes
          - elasticloadbalancing:RegisterInstancesWithLoadBalancer
          - elasticloadbalancing:DeregisterInstancesFromLoadBalancer
          - elasticloadbalancing:RemoveTags
          - autoscaling:DescribeAutoScalingGroups
          - autoscaling:DescribeInstanceRefreshes
          - ec2:CreateLaunchTemplate
          - ec2:CreateLaunchTemplateVersion
          - ec2:DescribeLaunchTemplates
          - ec2:DescribeLaunchTemplateVersions
          - ec2:DeleteLaunchTemplate
          - ec2:DeleteLaunchTemplateVersions
          Effect: Allow
          Resource:
          - '*'
        - Action:
          - autoscaling:CreateAutoScalingGroup
          - autoscaling:UpdateAutoScalingGroup
          - autoscaling:CreateOrUpdateTags
          - autoscaling:StartInstanceRefresh
          - autoscaling:DeleteAutoScalingGroup
          - autoscaling:DeleteTags
          Effect: Allow
          Resource:
          - arn:*:autoscaling:*:*:autoScalingGroup:*:autoScalingGroupName/*
        - Action:
          - iam:CreateServiceLinkedRole
          Condition:
            StringLike:
              iam:AWSServiceName: autoscaling.amazonaws.com
          Effect: Allow
          Resource:
          - arn:*:iam::*:role/aws-service-role/autoscaling.amazonaws.com/AWSServiceRoleForAutoScaling
        - Action:
          - iam:CreateServiceLinkedRole
          Condition:
            StringLike:
              iam:AWSServiceName: elasticloadbalancing.amazonaws.com
          Effect: Allow
          Resource:
          - arn:*:iam::*:role/aws-service-role/elasticloadbalancing.amazonaws.com/AWSServiceRoleForElasticLoadBalancing
        - Action:
          - iam:CreateServiceLinkedRole
          Condition:
            StringLike:
              iam:AWSServiceName: spot.amazonaws.com
          Effect: Allow
          Resource:
          - arn:*:iam::*:role/aws-service-role/spot.amazonaws.com/AWSServiceRoleForEC2Spot
        - Action:
          - iam:PassRole
          Effect: Allow
          Resource:
          - arn:*:iam::*:role/*.cluster-api-provider-aws.sigs.k8s.io
        - Action:
          - secretsmanager:CreateSecret
          - secretsmanager:DeleteSecret
          - secretsmanager:TagResource
          Effect: Allow
          Resource:
          - arn:*:secretsmanager:*:*:secret:aws.cluster.x-k8s.io/*
        Version: 2012-10-17
      Roles:
      - Ref: AWSIAMRoleControlPlane
    Type: AWS::IAM::ManagedPolicy
  AWSIAMRoleControlPlane:
    Properties:
      AssumeRolePolicyDocument:
        Statement:
        - Action:
          - sts:AssumeRole
          Effect: Allow
          Principal:
            Service:
            - ec2.amazonaws.com
        Version: 2012-10-17
      RoleName: control-plane.cluster-api-provider-aws.sigs.k8s.io
    Type: AWS::IAM::Role
  AWSIAMRoleNodes:
    Properties:
      AssumeRolePolicyDocument:
        Statement:
        - Action:
          - sts:AssumeRole
          Effect: Allow
          Principal:
            Service:
            - ec2.amazonaws.com
        Version: 2012-10-17
      RoleName: nodes.cluster-api-provider-aws.sigs.k8s.io
    Type: AWS::IAM::Role
```

### Install Host Requirements 
Before starting the Konvoy installation, verify that you have:
* An x86_64-based Linux machine with a supported version of the operating system
    * https://docs.d2iq.com/dkp/konvoy/2.0/supported-operating-systems/
* The dkp binary for Linux
    * Insert Download Link Here
* Docker version 18.09.2 or later.
* kubectl for interacting with the bootstrap and running clusters
    * Insert Kubectl Install Docker Credentials
* A valid AWS account with credentials configured
    * https://docs.d2iq.com/dkp/konvoy/2.0/choose_infrastructure/aws/iam-policies/


### External Repository Whitelisting
Please ensure that the external repositories are whitelisted prior to installing the cluster
```
.gcr.io
docker.elastic.co
docker-auth.elastic.co
.cloudfront.net
registry-1.docker.io
auth.docker.io
docker-images-prod.s3.dualstack.us-east-1.amazonaws.com
storage.googleapis.com
.ghcr.io
pkg-containers.githubusercontent.com
.quay.io
quayio-production-s3.s3.amazonaws.com
```
> Note: .domain.com denotes wildcard (*.domain.com)

## Installation
Kommmander cluster installation will take place in 3 steps:  
* Deploy Konvoy 2.X CLuster
* Deploy Kommander Application
* Run Post Installation Steps

It is recommended that 2 Terminal sessions are open to the install working directory (Install and Observation).  This will allow you to observe the deployment process while one terminal is waiting for commands to complete

### Konvoy Cluster Install Process
Konvoy 2.X now uses ClusterAPI do deploy Konvoy clusters in AWS.  For this proc4ess, we will create a KIND bootstrap cluster on the install host and then apply the deployment to AWS

Verify that the DKP binary is working correctly:
```
dkp --version
```

Set Env Variable for AWS Region:
```
export AWS_REGION=us-west-2
```

Set Your AWS Profile (if necessary):
```
export AWS_PROFILE=<profile>
```

Create Bootstrap KIND cluster on Node
```
dkp create bootstrap --kubeconfig $HOME/.kube/config
```

Set Cluster Name Variable:
```
CLUSTER_NAME=my-aws-cluster
```

Set Existing Infrastructure Variable Details:
```
export AWS_VPC_ID=<vpc-...>
export AWS_SUBNET_IDS=<subnet-...,subnet-...,subnet-...>
export AWS_ADDITIONAL_SECURITY_GROUPS=<sg-...>
export AWS_AMI_ID=<ami-...>
```

Send AWS Credentials to Bootstrap Cluster
```
dkp update bootstrap credentials aws
```

* Create Konvoy Cluster Deployment File
you will need access to a set of SSH keys to push to the endpoint
```
dkp create cluster aws --cluster-name=${CLUSTER_NAME} \
--vpc-id=${AWS_VPC_ID} \
--subnet-ids=${AWS_SUBNET_IDS} \
--additional-security-group-ids=${AWS_ADDITIONAL_SECURITY_GROUPS
--ssh-public-key-file=/complete/path/to/public/key.pub \
--ssh-username=centos \ # or appropriate user
--dry-run \
--output=yaml \
> ${CLUSTER_NAME}.yaml
```

Deploy Konvoy Custer to AWS
```
kubectl apply -f ${CLUSTER_NAME}.yaml
```

Wait for Control Plane to Become Available
```
kubectl wait --for=condition=ControlPlaneReady "clusters/${CLUSTER_NAME}" --timeout=20m
```

Observe the cluster deployment process (in second Terminal Session) with the following commands:
(make sure to set your "CLUSTER_NAME" variable)
```
KONVOY DEPLOYMENT OBSERVATION COMMANDS
dkp describe cluster -c ${CLUSTER_NAME}
kubectl get events | grep ${CLUSTER_NAME}
kubectl get clusters,kubeadmcontrolplanes,machinedeployments
kubectl --kubeconfig=${CLUSTER_NAME}.conf get nodes
```

### Kommander Install Process

Set the Variable of Kommander Version to be Installed
```
export VERSION=v2.0.0
```
Other possible Tags to use: `v0.0.0-dev-20211013011847-2d054f37` or `v2.1.0-beta.1`


Add and Update the Kommander Repo
```
helm repo add kommander https://mesosphere.github.io/kommander/charts
helm repo update
```

Verify AWS-EBS-CSI Provider is available and default
```
kubectl get sc
```

Deploy Kommander Helm Chart
```
helm install -n kommander --create-namespace kommander-bootstrap kommander/kommander-bootstrap --version=${VERSION} --set certManager=$(kubectl get ns cert-manager > /dev/null 2>&1 && echo "false" || echo "true")
```

Wait For Kommander to be Deployed
```
kubectl -n kommander wait --for condition=Released helmreleases --all --timeout 15m
```

Observe the Kommander deployment process (in second Terminal Session) with the following commands:
```
KOMMANDER OBSERVATION COMMANDS
```

Get Kommander Dashboard URL:
```
kubectl -n kommander get svc kommander-traefik -o go-template='https://{{with index .status.loadBalancer.ingress 0}}{{or .hostname .ip}}{{end}}/dkp/kommander/dashboard{{ "\n"}}'
```
and Credentials
```
kubectl -n kommander get secret dkp-credentials -o go-template='Username: {{.data.username|base64decode}}{{ "\n"}}Password: {{.data.password|base64decode}}{{ "\n"}}'
```

### Post Install Steps
* Create Kubectl Token from Kommander
* Verify all Kommander Components are Accessible & Working as expected
* Create desired "Workspaces" for testing
    * enable / disable Kommander addons as necessary

Proceed to deployingEdge Clusters


# Edge Konvoy Cluster Deployments

PreRequisites
Kommander Preparation
Edge Node Preparation
Edge Node Kubernetes Deployment






