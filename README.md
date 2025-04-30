# AWS Web CI/CD Deployment Project

This project demonstrates a simple Continuous Integration and Continuous Deployment (CI/CD) pipeline using GitHub and Jenkins to deploy a static website to an Nginx server hosted on AWS.

## 🚀 Project Overview

- Source code is hosted on GitHub.
- Jenkins is used to automatically build and deploy the site when changes are pushed to the repo.
- Nginx serves the static HTML page.
- The pipeline includes:
  - Cloning the GitHub repo
  - Cleaning the `/usr/share/nginx/html` directory
  - Deploying the `index.html` file to the server
  - Restarting Nginx

## 🔧 Tools & Tech Used

- GitHub
- Jenkins
- Nginx
- AWS EC2
- SSH
- Command Prompt (Windows)

## 📁 Files

- `index.html` — The main HTML file deployed via Jenkins
- `Jenkinsfile` — Defines the CI/CD pipeline steps

## ⚙️ Jenkins Pipeline Steps

```groovy
pipeline {
    agent any

    environment {
        WEB_SERVER = "your-ec2-ip"
        SSH_KEY = "/home/jenkins/.ssh/id_rsa"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/your-username/aws-web-cicd.git'
            }
        }
        stage('Clean Nginx Directory') {
            steps {
                sh "ssh -i $SSH_KEY $WEB_SERVER 'sudo rm -rf /usr/share/nginx/html/*'"
            }
        }
        stage('Deploy Site') {
            steps {
                sh "scp -i $SSH_KEY index.html $WEB_SERVER:/tmp/"
                sh "ssh -i $SSH_KEY $WEB_SERVER 'sudo mv /tmp/index.html /usr/share/nginx/html/'"
                sh "ssh -i $SSH_KEY $WEB_SERVER 'sudo systemctl restart nginx'"
            }
        }
    }
    post {
        success {
            echo '✅ Website deployed successfully!'
        }
        failure {
            echo '❌ Deployment failed. Check logs.'
        }
    }
}
