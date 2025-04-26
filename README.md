# Kustomize-fundamentals-with-Aws
This project showcases fundamentals of Kustomize and its integration with AWS services for managing Kubernetes configurations efficiently. Through hands-on exercises to gain practical experience in customizing deployment manifests, secrets management, and leveraging AWS resources in Kubernetes environments.

## "lesson-2.1:-kustomize-structure-and-concepts">Lesson 2.1: Kustomize Structure and Concepts

- Objective: Understand the directory structure, files, and key concepts of Kustomize.
1. Understanding Kustomize Structure:
- Base Directory: Contains the common, default resources for your application
- Overlays Directory: Contains environment-specific variations.
- Kustomization File:kustomization.yaml, used to declare the resources, bases, and overlays.

## Key Concepts:
- Resources: Kubernetes object manifests (e.g., deployments, services).
- Bases: Starting point of a configuration, often the generic version.
- Overlays: Modifications or environment-specific settings applied to the base.

## Activity</strong>:
- Create a basic directory structure:
myapp/
├── base/
│   └── deployment.yaml
│   └── kustomization.yaml
└── overlays/
    ├── dev/
    │   └── kustomization.yaml
    └── prod/
        └── kustomization.yaml
Populate deployment.yaml with a simple deployment manifest.

```yaml
   apiVersion: apps/v1    # API version for the deployment object
   kind: Deployment       # Specifies that this is a Deployment
   metadata:
   name: nginx-deployment  # Name of the deployment
   spec:                   # Specification of the deployment
   replicas: 2           # Number of replicas (pods running)
   selector:             # Selector to identify the pods
       matchLabels:
       app: nginx
   template:             # Template for the pod creation
       metadata:
       labels:
           app: nginx     # Label applied to the pod
       spec:
       containers:      
       - name: nginx    # Name of the container
           image: nginx:1.14.2  # Docker image to use for the container
           ports:
           - containerPort: 80  # Port the container will expose

```

2. Include the Deployment in kustomization.yaml
- In base/kustomization.yaml, reference the deployment:
```yaml
resources:
  - deployment.yaml # List of resource files to include in this configuration

```
3. Managing Kubernetes Objects:
Understand how to create and manage other common Kubernetes objects like services and pods using similar steps.

## Lesson 2.3: Basic Customization Techniques
- Tasks and Detailed Steps
1. Creating Overlays
- Create two overlays: dev and prod in respective directories.
- In overlays/dev/kustomization.yaml, reference the base and make a simple change like increasing the number of replicas.

Creating Overlays
2. Customization Example
- Dev overlay (overlays/dev/kustomization.yaml):
Copy bases:
  ```yaml 
  - ../../base # Path to the base configuration directory
patchesStrategicMerge:
  - replica_count.yaml 
 ```

 - In overlays/dev/replica_count.yaml, define the patch:
Copy
```yaml 
apiVersion: apps/v1 # API version for the deployment object
kind: Deployment # Specifies that this is a Deployment
metadata:
  name: nginx-deployment # Name of the deployment to patch
spec:
  replicas: 3  # Updating the number of replicas for the dev environment
```
3. Using Variables and Placeholders
- Discuss how Kustomize allows the use of variables and placeholders for dynamic configuration values refer to the [Kustomize documentation](https://kubectl.docs.kubernetes.io/references/kustomize/kustomization/vars/)

## Leveraging AWS
Expanding on leveraging AWS, specifically Amazon EKS (Elastic Kubernetes Service), for deploying and testing your Kustomize configurations requires a detailed step-by-step approach. This guide is designed for beginners and will walk you through the process from creating an EKS cluster to deploying your configurations.

- Set Up Your AWS Account and CLI
- Install AWS CLI
- Download and install the AWS CLI from the [official guide](https://aws.amazon.com/cli/)
- Configure the CLI with your credentials: Run `aws configure` and enter your AWS Access Key ID, Secret Access Key, and default region.

Step 2: Install and Configure  `eksctl`
`eksctl` is a simple CLI tool for creating clusters on EKS. It's the easiest way to get started with Amazon EKS.
1. Install `eksctl`:
- Follow the installation instructions on the [GitHub repository](https://github.com/eksctl-io/eksctl) `eksctl`.
2. Configure `eksctl`:
- No specific configuration is required post-installation. It works with your AWS CLI configuration.

Step 3: Create an EKS Cluster
- Run the following command to create a cluster (this can take several minutes):
`eksctl create cluster --name my-kustomize-cluster --version 1.18 --region us-east-1 --nodegroup-name my-nodes --node-type t2.medium --nodes 3`

This command creates an EKS cluster named "my-kustomize-cluster" with 3 nodes of type t2.medium.

- Verify Cluster Creation
Once the creation process is complete, verify your cluster with: `kubectl get svc`.

Step 4: Deploying Kustomize Configurations to EKS
1. Prepare Your Kustomize Configuration:
- Ensure your Kustomize configuration is ready in your project directory. For instance, you should have your "base" and "overlays" directories set up as per previous lessons.

2. Apply Configuration to EKS:
- Select the appropriate overlay for your deployment environment. For example, if deploying to a development environment, you might choose the dev  overlay.
- Run the following command from the root of your project directory:
`kubectl apply -k overlays/dev/`
- This applies the configurations defined in your dev overlay to the EKS cluster.

Step 5: Verify Deployment
- Check Deployed Resources:
- Run `kubectl get all` to see all the resources (like pods, services, deployments) that have been deployed to your cluster.
- You should see the resources defined in your Kustomize configurations.
- Troubleshoot if Needed:
If resources are not appearing as expected, use kubectl describe or kubectl logs commands to troubleshoot.

Step 6: Clean Up Resources
1. Delete Deployed Resources:
When you're done with testing, clean up your resources to avoid incurring unnecessary costs.
Run `kubectl delete -k overlays/dev/` to remove the resources.
2. Delete the EKS Cluster:
Remove your EKS cluster with: `eksctl delete cluster --name my-kustomize-cluster`
## Additional Resources and Considerations
- AWS Costs: Be mindful of AWS costs associated with EKS. Always clean up resources after use.
- EKS Documentation: Refer to the [EKS User Guide](https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html)  
For more detailed information.Security Practices: Follow best practices for AWS security, like using IAM roles and security groups effectively.

## Conclusion
This project provides a foundational understanding of Kustomize, its structure, and basic techniques for customizing Kubernetes resources. By implementing these in an AWS environment, learners gain practical skills that are highly relevant in modern cloud-native ecosystems.