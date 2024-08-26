# Terraform Kubernetes Deployment Challenge

## Overview

This challenge involves deploying a simple web application on an existing Kubernetes cluster using Terraform. The objective is to configure Terraform to deploy and manage Kubernetes resources, such as deployments and services.

## Architecture Diagram



## Challenge Details

In this challenge, we will utilize Terraform to deploy Kubernetes resources. If you're unfamiliar with Kubernetes, some concepts and resource schemas may be confusing. However, the goal is to build a solid foundation in deploying and managing Kubernetes resources using Terraform.

### Prerequisites

- **Terraform version:** 1.1.5
- **Kubernetes provider version:** 2.11.0
- **Kubernetes cluster:** Existing and configured with kubeconfig path at `/root/.kube/config`
- **Terraform Configuration Directory:** `/root/terraform_challenge`

### Steps to Complete the Challenge

1. **Check Terraform Installation**

   First, verify if Terraform is installed:

   ```bash
   which terraform
   ```

   If not installed, install Terraform:

   ```bash
   apt update
   apt install unzip -y
   curl -L -o /tmp/terraform_1.1.5_linux_amd64.zip https://releases.hashicorp.com/terraform/1.1.5/terraform_1.1.5_linux_amd64.zip
   unzip -d /usr/local/bin /tmp/terraform_1.1.5_linux_amd64.zip
   ```

2. **Configure Terraform and Provider Settings**

   Create a `provider.tf` file to configure the Kubernetes provider:

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

   Initialize the Terraform provider:

   ```bash
   terraform init
   ```

3. **Create Terraform Resources**

   Create a `main.tf` file to define the Kubernetes deployment and service resources:

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
             image = "kodekloud/webapp-color:v1"
             name  = "simple-webapp"
             port {
               container_port = 8080
             }
           }
         }
       }
     }
   }

   resource "kubernetes_service" "webapp-service" {
     metadata {
       name = "webapp-service"
     }
     spec {
       selector = {
         name = "webapp"
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

4. **Deploy the Resources**

   Plan and apply the Terraform configuration:

   ```bash
   terraform plan
   terraform apply
   ```

## Solution Summary

- **Step 1:** Verify Terraform installation and install if necessary.
- **Step 2:** Configure the Terraform provider in `provider.tf`.
- **Step 3:** Define Kubernetes deployment and service in `main.tf`.
- **Step 4:** Deploy the resources using `terraform plan` and `terraform apply`.

## Additional Resources

- [Terraform Documentation](https://www.terraform.io/docs/index.html)
- [Kubernetes Provider Documentation](https://registry.terraform.io/providers/hashicorp/kubernetes/latest/docs)
