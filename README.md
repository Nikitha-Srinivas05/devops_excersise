# DevOps Lab Exercises

A collection of hands-on DevOps lab exercises covering containerization, Kubernetes deployments, monitoring, security, and CI/CD automation using Jenkins. Each exercise includes implementation steps, commands, configurations, and results.

## Exercises

| No. | Exercise | Documentation | Source Code |
|---:|---|---|---|
| 1 | Running an Nginx Pod on Minikube | [EXERCISE1.md](EXERCISE1.md) | None (Commands only) |
| 2 | Flask Application Deployment on Minikube | [EXERCISE2.md](EXERCISE2.md) | [devops_exercise2/](devops_exercise2/) |
| 3 | Scaling a Flask Application Using ReplicaSets | [EXERCISE3.md](EXERCISE3.md) | [devops_exercise3/](devops_exercise3/) |
| 4 | Docker Networking with Multiple Containers | [EXERCISE4.md](EXERCISE4.md) | [devops_exercise4/](devops_exercise4/) |
| 5 | Docker Security Using AppArmor and Python | [EXERCISE5.md](EXERCISE5.md) | [devops_exercise5/](devops_exercise5/) |
| 6 | Delivery Monitoring with Prometheus and Grafana | [EXERCISE6.md](EXERCISE6.md) | [devops_exercise6/](devops_exercise6/) |
| 7 | Installing Jenkins Using Docker | [EXERCISE7.md](EXERCISE7.md) | None (Docker commands only) |
| 8 | Creating a Hello World Jenkins Job | [EXERCISE8.md](EXERCISE8.md) | [devops_exercise8/](devops_exercise8/) |
| 9 | Implementing a Multi-Stage Jenkins Pipeline | [EXERCISE9.md](EXERCISE9.md) | [devops_exercise9/](devops_exercise9/) |
| 10 | Multi-Node Kubernetes Cluster with Multiple Applications | [EXERCISE10.md](EXERCISE10.md) | [devops_exercise10/](devops_exercise10/) |

## Repository Structure

```text
devops_excersise/
├── README.md                              # Project overview, exercise index, and setup guide
├── .gitignore                             # Excludes unnecessary files and sensitive local configuration
│
├── EXERCISE1.md                           # Exercise 1: Run an Nginx Pod on Minikube
├── EXERCISE2.md                           # Exercise 2: Deploy a Flask application on Minikube
├── EXERCISE3.md                           # Exercise 3: Scale a Flask application using ReplicaSets
├── EXERCISE4.md                           # Exercise 4: Docker networking with multiple containers
├── EXERCISE5.md                           # Exercise 5: Container security using AppArmor and Python
├── EXERCISE6.md                           # Exercise 6: Monitor delivery metrics using Prometheus and Grafana
├── EXERCISE7.md                           # Exercise 7: Install and configure Jenkins using Docker
├── EXERCISE8.md                           # Exercise 8: Create a Hello World Jenkins job
├── EXERCISE9.md                           # Exercise 9: Build a multi-stage Jenkins pipeline
├── EXERCISE10.md                          # Exercise 10: Deploy multiple applications on Kubernetes
│
├── devops_exercise2/                      # Exercise 2: Flask application deployment
│   ├── app.py                             # Flask web application source code
│   ├── Dockerfile                         # Instructions for building the Flask Docker image
│   └── flask-deployment.yaml              # Kubernetes configuration for deploying the application
│
├── devops_exercise3/                      # Exercise 3: Flask application scaling
│   ├── app.py                             # Flask application used for ReplicaSet testing
│   ├── Dockerfile                         # Docker image configuration for the application
│   └── flashsale-replicaset.yaml          # Kubernetes ReplicaSet configuration
│
├── devops_exercise4/                      # Exercise 4: Docker networking
│   ├── app.py                             # Application used to test container communication
│   ├── Dockerfile                         # Instructions for building the application image
│   └── requirements.txt                   # Python dependencies required by the application
│
├── devops_exercise5/                      # Exercise 5: Docker security with AppArmor
│   ├── app.py                             # Python application for testing security restrictions
│   └── Dockerfile                         # Container image configuration for the security exercise
│
├── devops_exercise6/                      # Exercise 6: Delivery monitoring and alerting
│   ├── Dockerfile                         # Docker image configuration for the monitoring application
│   ├── Jenkinsfile                        # Jenkins pipeline configuration
│   ├── alert_rules.yml                    # Prometheus alerting rule definitions
│   ├── delivery_metrics.py                # Application that exposes delivery-related metrics
│   ├── prometheus.yml                     # Prometheus scraping and monitoring configuration
│   ├── requirements.txt                   # Python dependencies for the monitoring application
│   │
│   └── jenkins-docker/                    # Custom Jenkins Docker image configuration
│       └── Dockerfile                     # Instructions for building the Jenkins image
│
├── devops_exercise8/                      # Exercise 8: Jenkins freestyle job
│   └── hello-world.sh                     # Shell script executed by the Jenkins job
│
├── devops_exercise9/                      # Exercise 9: Jenkins pipeline and Python testing
│   ├── app.py                             # Flask application built and tested by the pipeline
│   ├── requirements.txt                   # Python application and testing dependencies
│   └── test_app.py                        # Automated tests for the Flask application
│
├── devops_exercise10/                     # Exercise 10: Multiple Kubernetes applications
│   ├── product_catalog.py                 # Product Catalog application source code
│   ├── shopping_cart.py                   # Shopping Cart application source code
│   ├── Dockerfile.product                 # Docker image definition for Product Catalog
│   ├── Dockerfile.shopping                # Docker image definition for Shopping Cart
│   ├── product_catalog_deployment.yaml    # Kubernetes Deployment for Product Catalog
│   ├── product_catalog_service.yaml       # Kubernetes Service for Product Catalog
│   ├── shopping_cart_deployment.yaml      # Kubernetes Deployment for Shopping Cart
│   └── shopping_cart_service.yaml         # Kubernetes Service for Shopping Cart
│
└── images/                                # Screenshots documenting exercise results
```

## Technologies Used

- **Docker** – Building images and running containerized applications.
- **Kubernetes** – Deploying applications, managing replicas, and exposing services.
- **Minikube** – Running local Kubernetes clusters.
- **Python and Flask** – Developing web applications and REST APIs.
- **YAML** – Defining Kubernetes Deployments and Services.
- **AppArmor** – Exploring container security.
- **Prometheus** – Collecting application metrics.
- **Grafana** – Visualizing monitoring data through dashboards.
- **Jenkins** – Automating jobs, builds, testing, and CI/CD pipelines.
- **Git and GitHub** – Version control and source code management.

## Prerequisites

Install the tools required for the exercise you want to run:

- Docker Desktop
- Minikube
- kubectl
- Python 3.9 or later
- Git
- Jenkins
- Prometheus and Grafana, where applicable

## Learning Outcomes

- Building and managing Docker images.
- Deploying and scaling applications with Kubernetes.
- Configuring multi-node clusters and pod anti-affinity.
- Exposing applications through Kubernetes Services.
- Exploring container security and monitoring.
- Creating Jenkins jobs and CI/CD pipelines.
- Documenting and managing projects with Git and GitHub.

## Notes

- Exercises 1 and 7 primarily use commands documented in their respective Markdown files and do not have dedicated application-code folders.
- Exercises 2–6 and 8–10 have supporting files in their respective directories.
- Each EXERCISEn.md file contains the instructions for its corresponding exercise.
- The images/ directory contains screenshots that help demonstrate implementation and testing results.
