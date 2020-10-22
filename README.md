# Hybrid_Task_6

## Deploy the Wordpress application on Kubernetes and AWS using terraform
##Problem Statement:
Deploy the Wordpress application on Kubernetes and AWS using terraform including the following steps:-

-> Write an Infrastructure as code using terraform, which automatically deploy the Wordpress application
-> On AWS, use RDS service for the relational database for Wordpress application.
-> Deploy the Wordpress as a container either on top of Minikube or EKS or Fargate service on AWS
-> The Wordpress application should be accessible from the public world if deployed on AWS or through workstation if deployed on Minikube.
## Prerequisites:
-> AWS CLIv2 installed and configured with IAM user.
-> Minikube, Terraform installed.
-> kubectl configured.
# Implementation:
## Step 1: Write an Infrastructure as code using terraform, which automatically deploy the Wordpress application:
```
provider "kubernetes" {
  config_context_cluster   = "minikube"
}
resource "kubernetes_deployment" "wp" {
  metadata {
    name = "wordpress"
    labels = {
      app = "wordpress"
    }
  }
spec {
    replicas = 1
selector {
      match_labels = {
        app = "wordpress"
      }
    }
template {
      metadata {
        labels = {
          app = "wordpress"
        }
      }
spec {
        container {
          image = "wordpress"
          name  = "wp"
        }
      }
    }
  }
}
```

## Step 2 : On AWS, use RDS service for the relational database for Wordpress application:
```
resource "aws_security_group" "mysql-rds" {
name        = "RDS-Security-Group"
  description = "MySQL Ports"
 
  ingress {
    description = "Mysql RDS"
    from_port   = 3306
    to_port     = 3306
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
tags = {
    Name = "Mysql-RDS"
  }
}
resource "aws_db_instance" "default" {
  allocated_storage    = 20
  storage_type         = "gp2"
  engine               = "mysql"
  engine_version       = "5.7"
  instance_class       = "db.t2.micro"
  name                 = "mydb"
  username             = "user1"
  password             = "password"
  parameter_group_name = "default.mysql5.7"
  publicly_accessible = true
  skip_final_snapshot = true
  vpc_security_group_ids = [aws_security_group.mysql-rds.id]
  tags = {
  name = "RDS"
   }
}
```

## Step 3: Deploy the Wordpress as a container either on top of Minikube or EKS or Fargate service on AWS:
```
resource "kubernetes_service" "wp-expose"{
  depends_on = [kubernetes_deployment.wp]
  metadata {
    name = "wp-expose"
  }
  spec {
    selector = {
      app = kubernetes_deployment.wp.metadata.0.labels.app
    }
    port {
      node_port = 30001
      port        = 80
      target_port = 80
    }
    type = "NodePort"
  }
}
```

## Step 4: The Wordpress application should be accessible from the public world if deployed on AWS or through workstation if deployed on Minikube.
# Command to run code:
-> Run the Terraform Code.
```
terraform init
```
-> Apply Terraform.
```
terraform apply -auto-approve
```

-> Output
alt text

alt text

### Wordpress Setup
alt text

alt text

alt text

## - Now you need to provide the DB, user, password, and for Database Host use RDS Endpoint.
alt text

alt text

alt text

alt text


## - And at the end delete or destroy the complete process.
```
terraform destroy -auto-approve
```
alt text

## Thank You!!!
