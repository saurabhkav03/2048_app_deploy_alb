Sure, here are the delete instructions in raw format suitable for adding to a GitHub README:

```
1. **Delete the Deployment, Service, and Ingress**:
   ```sh
   kubectl delete -f https://raw.githubusercontent.com/saurabhkav03/2048_app_deploy_alb/main/2048-full.yml
   ```

2. **Delete the Fargate Profile**:
   ```sh
   eksctl delete fargateprofile --cluster <cluster_name> --name <fargate_name> --region <region_code>
   ```

3. **Delete the ALB Ingress Controller**:
   - First, delete the ALB controller deployment:
     ```sh
     kubectl delete deployment -n kube-system aws-load-balancer-controller
     ```
   - Then, delete the IAM Role and Policy created for the ALB controller:
     ```sh
     aws iam delete-policy --policy-arn arn:aws:iam::<your-aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy
     eksctl delete iamserviceaccount --cluster <your-cluster-name> --namespace kube-system --name aws-load-balancer-controller
     ```

4. **Delete the EKS Cluster**:
   ```sh
   eksctl delete cluster --name my-cluster --region <region_code>
   ```

```

These instructions provide the commands necessary to delete the resources created during the deployment of the 2048 App on Amazon EKS. Replace placeholders like `<cluster_name>`, `<fargate_name>`, `<region_code>`, and `<your-aws-account-id>` with appropriate values based on your setup.