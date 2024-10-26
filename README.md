# 🐳 Example Voting App CI/CD with Azure DevOps

This repository contains the **CI/CD pipeline configuration** for the [Example Voting App](https://github.com/dockersamples/example-voting-app) project, managed with **Azure DevOps**. The goal is to automate building, pushing, and deploying images to an Azure Kubernetes Service (AKS) cluster using a **GitOps** approach with **ArgoCD**.

---

## 📂 Project Structure

This repository uses the open-source Example Voting App, which I have integrated with my Azure DevOps project. Major changes include:
- **Result App Pipeline (`result_app_pipeline.yaml`)**: For building and deploying the result service.
- **Voting App Pipeline (`voting_app_pipeline.yaml`)**: For managing the voting service.
- **Worker App Pipeline (`worker_app_pipeline.yaml`)**: For the background worker service.
- **Shell Script (`updatek8sspeci.sh`)**: A script to update Kubernetes specifications automatically.

---

## 🛠️ Prerequisites

- **Azure DevOps Account**: For creating pipelines and managing repositories.
- **Azure Container Registry (ACR)**: Created with the name `khizraghaffarCICD`.
- **Linux VM on Azure**: For running the pipeline agent.
- **Azure Kubernetes Service (AKS)**: To deploy the application.
- **ArgoCD**: For GitOps-based continuous deployment.

---

## 🚀 CI/CD Pipeline Setup

### Step 1: Create Azure Resources

1. **Azure Container Registry (ACR)**: Set up ACR with the name `khizraghaffarCICD`.
2. **Linux VM**: Create a Linux VM to act as an agent for your Azure pipeline. 
    - Save the **private key** for SSH access.

---

### Step 2: Configure the Azure DevOps Pipeline

1. **Create a Pipeline**:
   - Go to your **Azure DevOps** project.
   - Set up pipelines for each of the microservices (result, voting, worker) by using the respective YAML files (`result_app_pipeline.yaml`, `voting_app_pipeline.yaml`, `worker_app_pipeline.yaml`).

2. **Agent Pool Setup**:
   - Log in to the VM using:
     ```bash
     ssh -i <your-key-file> azureuser@<vm-public-ip>
     ```
   - Register the VM as an agent by providing your **organization URL**:
     ```plaintext
     https://dev.azure.com/{yourorganization}  #in my case it is https://dev.azure.com/khghaffa23
     ```
   - Enter your **personal access token** (PAT) from Azure DevOps.
   - Start the agent with:
     ```bash
     ./run.sh
     ```

3. **Pipeline Stages**:
   - **Build**: Builds a Docker image.
   - **Push**: Pushes the image to ACR.
   - **Update**: Runs a shell script (`updatek8sspeci.sh`) to modify Kubernetes specifications.

---

### 🌀 GitOps: Continuous Delivery with ArgoCD

1. **Set Up Kubernetes Service**:
   - Create an AKS cluster in Azure to host the application.

2. **Install ArgoCD**:
   - Log into your Kubernetes cluster and install ArgoCD:
     ```bash
     kubectl create namespace argocd
     kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
     ```
   - Verify installation:
     ```bash
     kubectl get pods -n argocd
     ```

3. **Configure ArgoCD**:
   - Link ArgoCD to the Azure DevOps repository to monitor changes and automatically update the AKS cluster when new images are available.
4. `updatek8sspeci.sh` **Script**:
   - This script pulls the repository, updates the Kubernetes specifications with the new image tag, and pushes changes back.

---
### 🔍 Verifying the Deployment
To check the status of your application in AKS:
  ```bash
  kubectl get pods
  ```

### 🤖 Additional Resources
- Azure DevOps Documentation 
- ArgoCD Documentation
- Kubernetes Documentation

### 📢 Note
**GitOps Benefits:**

- GitOps continuously monitors the Azure repository, automatically deploying changes to the Kubernetes cluster.
- **Security:** GitOps `restricts` direct modifications to Kubernetes manifests, offering a reconciliation process to maintain a desired state.
