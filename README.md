# ?? **CI/CD Pipeline: Deploying a Python App to AWS ECS using GitHub Actions & Terraform**  

![GitHub Actions to AWS ECS](https://i.imgur.com/CDhxva2.png)

---

## ?? **Overview**  

This project automates the deployment of a **Python application** to **AWS Elastic Container Service (ECS)** using **GitHub Actions, Docker, and Terraform**.  

By the end of this guide, you'll have a fully automated **CI/CD pipeline** that:  
? **Builds a Docker image** of the Python app  
? **Pushes the image to AWS Elastic Container Registry (ECR)**  
? **Deploys the containerized app to ECS (Fargate)**  
? **Uses Terraform to provision and manage AWS infrastructure**  

---

## ?? **Project Goals**  

? **Develop a Python-based web application**  
? **Containerize the app using Docker and push it to AWS ECR**  
? **Deploy the app to AWS ECS using Terraform**  
? **Automate testing, building, and deployment using GitHub Actions**  

---

## ?? **Detailed Project Structure**

```sh
TF-ECR-ECS-GH-Deploy/
¦-- .github/
¦   +-- workflows/        # GitHub Actions workflows
¦       +-- apply.yml     # Workflow to apply Terraform changes
¦       +-- destroy.yml   # Workflow to destroy Terraform resources
¦       +-- workflow.yml  # Main CI/CD workflow
¦
¦-- terraform/            # Terraform configurations for AWS resources
¦
¦-- .dockerignore         # Exclude unnecessary files from Docker builds
¦-- app.py                # Python application (Flask/Django/FastAPI)
¦-- Dockerfile            # Docker configuration for building the application
¦-- README.md             # Project documentation
¦-- requirements.txt      # Python dependencies for the application
```

---

## ?? **Prerequisites**  

Ensure the following tools are installed:  

| Tool | Version | Purpose |
|------|---------|---------|
| **AWS CLI** | Latest | Manage AWS services |
| **Terraform** | Latest | Infrastructure as Code |
| **Docker** | Latest | Build and manage containers |
| **GitHub Actions** | N/A | Automate CI/CD |
| **Python 3** | Latest | Run the application |

?? **Configure AWS CLI with your credentials:**  

```sh
aws configure
```

---

## ?? **GitHub Secrets Configuration**  

Store sensitive AWS credentials in GitHub:  

1?? Navigate to **Settings** ? **Secrets and Variables** ? **Actions**  
2?? Click **New repository secret**  
3?? Add the following secrets:  

| Secret Name | Value |
|------------|-------|
| `AWS_ACCESS_KEY_ID` | Your AWS Access Key |
| `AWS_SECRET_ACCESS_KEY` | Your AWS Secret Key |
| `AWS_REGION` | AWS Region (e.g., `us-east-1`) |
| `ECR_REPOSITORY` | Your AWS ECR Repository Name |
| `ECS_CLUSTER_NAME` | Your ECS Cluster Name |
| `ECS_SERVICE_NAME` | Your ECS Service Name |

---

## ?? **Terraform Configuration**  

Terraform provisions AWS infrastructure, including:  

? **VPC, Subnets, Security Groups**  
? **AWS ECR** (Container registry)  
? **AWS ECS Cluster** & **Fargate Service**  
? **IAM Roles & Policies**  

Example Terraform configuration:  

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_ecr_repository" "app" {
  name = "my-python-app"
}

resource "aws_ecs_cluster" "main" {
  name = "my-cluster"
}
```

?? Run Terraform commands to apply changes:  

```sh
terraform init
terraform apply -auto-approve
```

---

## ?? **GitHub Actions CI/CD Workflow**  

?? **Workflow File Location:**  

- `.github/workflows/workflow.yml`

### **?? Workflow Overview**  

#### ? **Triggers**  

- Runs on `push` or `pull_request` events to `main` branch  

#### ? **Job 1: Build & Push Docker Image**  

- Builds the **Docker image** and pushes it to **AWS ECR**  

#### ? **Job 2: Deploy to AWS ECS**  

- Updates the ECS Service to use the new container  

---

## ?? **Step-by-Step Breakdown of CI/CD Pipeline**  

### 1?? **Checkout Repository**  

```yaml
- name: Checkout code
  uses: actions/checkout@v3
```

---

### 2?? **Configure AWS Credentials**  

```yaml
- name: Configure AWS Credentials
  uses: aws-actions/configure-aws-credentials@v2
  with:
    aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
    aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
    aws-region: ${{ secrets.AWS_REGION }}
```

---

### 3?? **Login to AWS ECR**  

```yaml
- name: Login to AWS ECR
  run: |
    aws ecr get-login-password --region ${{ secrets.AWS_REGION }} | docker login --username AWS --password-stdin ${{ secrets.ECR_REPOSITORY }}
```

---

### 4?? **Build & Push Docker Image**  

```yaml
- name: Build, Tag, and Push Image to ECR
  run: |
    docker build -t ${{ secrets.ECR_REPOSITORY }}:latest .
    docker tag ${{ secrets.ECR_REPOSITORY }}:latest ${{ secrets.ECR_REPOSITORY }}:latest
    docker push ${{ secrets.ECR_REPOSITORY }}:latest
```

---

### 5?? **Deploy to AWS ECS**  

```yaml
- name: Deploy to AWS ECS
  run: |
    aws ecs update-service --cluster ${{ secrets.ECS_CLUSTER_NAME }} --service ${{ secrets.ECS_SERVICE_NAME }} --force-new-deployment
```

---

## ?? **Monitoring Deployment**  

?? **How to check deployment progress:**  

1?? **GitHub Actions Logs:** Navigate to **Actions** tab  
2?? **AWS ECS Console:** Check service & tasks status  
3?? **AWS CloudWatch Logs:** View application logs  

---

## ? **Final Testing**  

Once deployment is successful, test the application:  

```sh
curl http://your-ecs-service-url
```

---

## ?? **Conclusion**  

?? **You’ve successfully deployed a Python app to AWS ECS using GitHub Actions & Terraform!**  

? **Fully automated CI/CD pipeline**  
? **Scalable & secure AWS infrastructure**  
? **Seamless GitHub Actions integration**  

---
