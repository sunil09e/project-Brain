# 🚀 Brain Tasks App Deployment using AWS EKS & CI/CD

# 📌 Project Overview

This project demonstrates deploying a React application to a production-ready Kubernetes environment using AWS services and CI/CD pipeline.

The application is built and served using NGINX, containerized with Docker, pushed to AWS ECR, and deployed on Amazon EKS.



# 🛠️ Technologies Used

- React.js (Frontend)

- Docker (Containerization)

- NGINX (Static file server)

- AWS ECR (Container Registry)

- Amazon EKS (Kubernetes Cluster)

- Kubernetes (kubectl)

- AWS CodeBuild (Build automation)

- AWS CodePipeline (CI/CD)

- AWS CloudWatch (Monitoring)



        
   
# 📂 Project Structure

.

├── Dockerfile 

├── buildspec.yml 

├── deployment.yaml

├── service.yaml

├── aws-auth.yaml

├── dist/                  # React production build

└── README.md


     
# ⚙️ Setup Instructions

**🔹 Clone Repository**
   git clone https://github.com/Vennilavanguvi/Brain-Tasks-App.git

   cd Brain-Tasks-App


**🔹 Build Docker Image**

  docker build -t brain-tasks-app .

  docker run -p 3000:80 brain-tasks-app

  👉 Open: http://localhost:3000


**🔹 Push Image to ECR**

  aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin <ECR_URL>

  docker tag brain-tasks-app:latest <ECR_URL>/brain-task:latest

  docker push <ECR_URL>/brain-task:latest


# ☸️ Kubernetes (EKS)

 **🔹Cluster Setup**

  eksctl create cluster --name brain-cluster --region ap-south-1 --node-type t2.medium --nodes 2
  
  aws eks update-kubeconfig --region ap-south-1 --name brain-cluster
  
  kubectl get nodes

 **🔹Deployment**

  kubectl apply -f deployment.yaml

  kubectl apply -f service.yaml

 **🔹Access Application**

kubectl get svc

👉 Copy EXTERNAL-IP and open in browser

# 🔧 CodeBuild

**🛠️ Steps to Setup CodeBuild**

**🔹 1. Create CodeBuild Project**

Go to AWS Console → CodeBuild

Click Create Build Project

**🔹 2. Configure Source**

Source provider: GitHub

Connect your repository

Select the repository and branch

**🔹 3. Configure Environment**

Environment type: Managed Image

OS: Amazon Linux / Ubuntu

Runtime: Standard

Enable Privileged Mode ✅ (required for Docker)

Privileged mode allows Docker commands inside CodeBuild

**🔹 4. Configure Service Role**

Create or select IAM role

Permissions required:

- ECR / DockerHub access

- EKS access

- CloudWatch logs
 
**🔹 5. Add Buildspec File**

Create a file named buildspec.yml in your project:

**🔹6. Build Execution**

CodeBuild automatically runs when triggered by CodePipeline

Executes all commands in buildspec.yml

**🔐 aws-auth ConfigMap**

Maps IAM roles to Kubernetes RBAC

Allows:

Worker nodes to join cluster

CodeBuild to deploy resources


# 🔄 CodePipeline

**Pipeline Configuration Steps**

**🔹 Step 1: Choose Pipeline Type**

Select: Build custom pipeline

**🔹 Step 2: Define Pipeline Settings**

Enter the pipeline name.

Service role configured for permissions

**🔹 Step 3: Add Source Stage**

Connected GitHub repository

Selected branch (main/master)

Enabled automatic trigger on code push

**🔹 Step 4: Add Build Stage**

Integrated with AWS CodeBuild

Used buildspec.yml to define build steps

**🔹Step 5: Add Deploy Stage**

**🔹 Deploy Provider**

Select: Amazon EKS

👉 This enables direct deployment to the Kubernetes cluster managed by AWS

**🔹 Region**

Selected: Asia Pacific (Mumbai)

👉 Ensures deployment happens in the same region where EKS cluster is hosted

**🔹 Input Artifacts**

Use: SourceArtifact

👉 This contains the application code and Kubernetes YAML files from the source stage

**🔹 EKS Cluster**

Selected the created EKS cluster

👉 This is the target environment where the application is deployed

**🔹 Deployment Type**

Selected: Kubectl

👉 Reason:

Allows direct execution of Kubernetes commands

Uses YAML files for deployment

Provides flexibility compared to Helm

**🔹 Manifest Files**

deployment.yaml, service.yaml

👉 These files define:

Deployment → Application pods

Service → Exposes application using LoadBalancer

**Pipeline flow:**

GitHub → CodeBuild → ECR → EKS

**🔹 Pipeline Stages**

**1. Source Stage**

       Fetch code from GitHub

**2. Build Stage (CodeBuild)**

       Build Docker image
       
       Push image to ECR
       
       Update Kubernetes deployment

**3. Deploy Stage**
    
        Deploy application to EKS

**⚠️ Key Rule**

👉CodePipeline region MUST be same as EKS cluster region


# 📊 Monitoring (CloudWatch Logs)

**🔹 Build Logs**

/aws/codebuild/newproject

**🔹 Pipeline Logs**

/aws/codepipeline/project-1

**🔹 Application Logs**

Viewed using:

kubectl logs <POD_NAME>


# 🌐 Application Access

LoadBalancer URL:
http://abfdd0cb6ea7b487cb4997762fe053df-1603066755.ap-south-1.elb.amazonaws.com


# 🔑 Key Points

- Docker is used to containerize the application 

- ECR stores Docker images 

- EKS runs containers as pods 

- CodeBuild automates build and push

- CodePipeline automates CI/CD workflow

- LoadBalancer exposes application to internet

- CloudWatch is used for monitoring logs

- Kubernetes manages deployment and scaling


# ✅ Conclusion

This project demonstrates a complete CI/CD pipeline with containerized application deployment on AWS EKS.
