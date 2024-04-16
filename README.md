
# Deploying 2048 App on Amazon EKS

## Prerequisites

1. **Install kubectl**: 
   - Follow the instructions at [Install and Set Up kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/).

2. **Install eksctl**: 
   - Follow the instructions at [Setting Up eksctl](https://docs.aws.amazon.com/emr/latest/EMR-on-EKS-DevelopmentGuide/setting-up-eksctl.html#setting-up-eksctl-linux) or [eksctl GitHub](https://github.com/eksctl-io/eksctl).

3. **Install AWS CLI**: 
   - Follow the instructions at [AWS CLI User Guide](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html).
   - After installation, run `aws configure` and provide Access Key ID, Secret Access Key, Default region, and output format.

4. **Create EKS cluster**: 
   - Run the command:
     ```sh
     eksctl create cluster --name my-cluster --region region-code --fargate
     ```

   *This may take some time.*

5. **Configuring kubectl for EKS**: 
   - Once the EKS cluster is created, configure it to work with your EKS cluster.
   - Go to the EKS service in the AWS Management Console and select your cluster.
   - Click on the "Config" button and follow the instructions to update your kubeconfig file.
   - Alternatively, use the AWS CLI to update the kubeconfig file:
     ```sh
     aws eks update-kubeconfig --name your-cluster-name --region region-code
     ```
   - Verify the configuration by running a kubectl command against your EKS cluster:
     ```sh
     kubectl get nodes
     ```

## Deploying 2048 App

1. **Create Fargate profile**:
   - Run the command:
     ```sh
     eksctl create fargateprofile --cluster <cluster_name> --region <region_code> --name <fargate_name> --namespace game-2048
     ```

2. **Deploy the app**:
   - Apply the deployment, service, and ingress YAML file:
     ```sh
     kubectl apply -f https://github.com/saurabhkav03/2048_app_deploy_alb/2048.yml
     ```

3. **Create and deploy ALB ingress controller**:
   - **Prerequisite**: Configure IAM OIDC provider before adding ALB controller:
     ```sh
     eksctl utils associate-iam-oidc-provider --cluster $cluster_name --approve
     ```
   - **Install ALB controller**:
     1. Download IAM policy:
        ```sh
        curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/install/iam_policy.json
        ```
     2. Create IAM Policy:
        ```sh
        aws iam create-policy --policy-name AWSLoadBalancerControllerIAMPolicy --policy-document file://iam_policy.json
        ```
     3. Create IAM Role:
        ```sh
        eksctl create iamserviceaccount --cluster=<your-cluster-name> --namespace=kube-system --name=aws-load-balancer-controller --role-name AmazonEKSLoadBalancerControllerRole --attach-policy-arn=arn:aws:iam::<your-aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy --approve
        ```
     4. Deploy ALB controller using Helm:
        - Add helm repo:
          ```sh
          helm repo add eks https://aws.github.io/eks-charts
          ```
        - Update the repo:
          ```sh
          helm repo update eks
          ```
        - Install:
          ```sh
          helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system --set clusterName=<your-cluster-name> --set serviceAccount.create=false --set serviceAccount.name=aws-load-balancer-controller --set region=<region> --set vpcId=<your-vpc-id>
          ```

   - **Verify deployments**:
     ```sh
     kubectl get deployment -n kube-system aws-load-balancer-controller
     ```

