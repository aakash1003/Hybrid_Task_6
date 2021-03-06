# Hybrid_Task_6

Deploy the Wordpress application on Kubernetes and AWS using terraform:

## Problem Statement
Deploy the Wordpress application on Kubernetes and AWS using terraform including the following steps;

1. Write an Infrastructure as code using terraform, which automatically deploy the Wordpress application
2. On AWS, use RDS service for the relational database for Wordpress application.
3. Deploy the Wordpress as a container either on top of Minikube or EKS or Fargate service on AWS
4. The Wordpress application should be accessible from the public world if deployed on AWS or through workstation if deployed on Minikube.
5. Try each step first manually and write Terraform code for the same to have a proper understanding of workflow of task.

# Solution for this problem is:
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
Command to run whole code.
```
terraform init
```
![alt text](/09.PNG)

```
terraform apply --auto-approve
```
![alt text](/10.PNG)

### Output:


![alt text](/08.PNG)

### Wordpress:

![alt text](/01.PNG)
![alt text](/02.PNG)
![alt text](/03.PNG)
![alt text](/04.PNG)
![alt text](/05.PNG)
![alt text](/06.PNG)
![alt text](/07.PNG)











To destroy all
```
terraform destroy --auto-approve
```

### Built With
Terraform
AWS
### Author
Aakash Sharma
