# 🚀 KodeKloud Lab – Terraform Challenge 1  

This repository contains the solution for **KodeKloud Terraform Challenge 1**.  
In this challenge, we install and configure Terraform, then deploy several Kubernetes resources (a Deployment and a Service) using Infrastructure as Code (IaC).  

---

## 🗺️ Architecture Diagram  

<img width="854" height="602" alt="1" src="https://github.com/user-attachments/assets/4871252b-08d2-4062-9751-0ec8f3ef8e72" />

---

## 🛠️ Deployment Steps  

### **✅ Step 1 – Install Terraform (v1.1.5) on the Controlplane**  

1. **Check if Terraform is installed**:  
   ```bash
   terraform --version
   ```
   - No output means Terraform is not installed.  

2. **Update the OS, install unzip, and install Terraform v1.1.5**:  
   ```bash
   apt update
   apt install unzip -y
   curl -L -o /tmp/terraform_1.1.5_linux_amd64.zip https://releases.hashicorp.com/terraform/1.1.5/terraform_1.1.5_linux_amd64.zip
   unzip -d /usr/local/bin /tmp/terraform_1.1.5_linux_amd64.zip
   ```

---

### **✅ Step 2 – Configure Terraform and Provider Settings (`provider.tf`)**  

1. **Create the provider configuration file**:  
   ```bash
   cd /root/terraform_challenge
   vi provider.tf
   ```

2. **Add the following configuration**:  
   ```hcl
   terraform {
     required_providers {
       kubernetes = {
         source  = "hashicorp/kubernetes"
         version = "2.11.0"
       }
     }
   }

   provider "kubernetes" {
     config_path = "/root/.kube/config"
   }
   ```

3. **Initialize Terraform**:  
   ```bash
   terraform init
   ```

---

### **✅ Step 3 – Create Terraform Resources**  

#### 1. **Kubernetes Service (`webapp-service.tf`)**  
```hcl
resource "kubernetes_service" "webapp-service" {
  metadata {
    name = "webapp-service"
  }
  spec {
    selector = {
      name = kubernetes_deployment.frontend.spec.0.template.0.metadata.0.labels.name
    }
    port {
      port        = 8080
      target_port = 8080
      node_port   = 30080
    }
    type = "NodePort"
  }
}
```

#### 2. **Kubernetes Deployment (`frontend.tf`)**  
```hcl
resource "kubernetes_deployment" "frontend" {
  metadata {
    name = "frontend"
    labels = {
      name = "frontend"
    }
  }
  spec {
    replicas = 4
    selector {
      match_labels = {
        name = "webapp"
      }
    }
    template {
      metadata {
        labels = {
          name = "webapp"
        }
      }
      spec {
        container {
          name  = "simple-webapp"
          image = "kodekloud/webapp-color:v1"
          port {
            container_port = 8080
          }
        }
      }
    }
  }
}
```

---

### **✅ Step 4 – Deploy and Verify**  

1. **Apply the Terraform configuration**:  
   ```bash
   terraform plan
   terraform apply
   ```

2. **Verify the deployment**:  
   ```bash
   kubectl get all
   ```

3. *(Or click the lab’s check button to validate your solution!)*  

🎉 Congratulations – your Kubernetes resources are now deployed via Terraform!  

<img width="1919" height="814" alt="2" src="https://github.com/user-attachments/assets/1f3ed5a4-91ca-43d9-96e0-2857900c0c9b" />

---

## 🧠 Contributions  
Contributions, corrections, and improvements are welcome.  
Feel free to open a **pull request** or create an **issue**.  

---

## ✍️ Author  
Made with 💻 by **Nidhal Labri**  
🔗 [LinkedIn](https://www.linkedin.com/in/nidhal-labri/)  
