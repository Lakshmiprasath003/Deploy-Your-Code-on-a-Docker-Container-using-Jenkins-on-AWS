# React + TypeScript + Vite – Task - Manager - Dashboard

A modern Kanban Dashboard application built using **React, TypeScript, and Vite**. This project demonstrates frontend development and automated CI/CD deployment using Jenkins, Docker, and AWS EC2.

## 🚀 Tech Stack

- **Frontend:** React, TypeScript
- **Build Tool:** Vite
- **Containerization:** Docker
- **CI/CD:** Jenkins
- **Version Control:** Git & GitHub
- **Cloud Platform:** AWS EC2
- **Automation:** GitHub Webhooks

## ✨ Features

- React-based frontend application
- TypeScript for type safety
- Fast development with Vite and HMR
- ESLint configuration for code quality
- Docker-based application deployment
- Automated Jenkins CI/CD pipeline

## 🏗️ CI/CD Pipeline

Implemented an automated CI/CD workflow:

```text
Developer
   |
   v
GitHub Repository
   |
   v
GitHub Webhook
   |
   v
Jenkins Pipeline
   |
   v
Source Code Checkout
   |
   v
Docker Image Build
   |
   v
Docker Hub Registry
   |
   v
AWS EC2 Deployment
   |
   v
Running Application
```

## ⚙️ CI/CD Implementation

- Configured GitHub Webhooks for automatic Jenkins build triggering.
- Integrated GitHub with Jenkins.
- Automated source code checkout.
- Built Docker images through Jenkins Pipeline.
- Pushed Docker images to Docker Hub.
- Deployed Docker containers on AWS EC2.
- Validated the application after deployment.

## 💻 Run Locally

Clone the repository:

```bash
git clone <YOUR-GITHUB-REPOSITORY-URL>
cd <PROJECT-FOLDER>
```

Install dependencies:

```bash
npm install
```

Start development server:

```bash
npm run dev
```

Build for production:

```bash
npm run build
```

## 🐳 Docker Deployment

Build Docker image:

```bash
docker build -t kanban-dashboard .
```

Run Docker container:

```bash
docker run -d -p 8080:80 --name kanban-dashboard kanban-dashboard
```

## ☁️ AWS Deployment

The application is deployed on an AWS EC2 instance using Docker containers through an automated Jenkins CI/CD pipeline.

## 🎯 Project Objectives

- Understand React and TypeScript application structure.
- Learn Docker image creation and container deployment.
- Implement Jenkins pipeline automation.
- Integrate GitHub Webhooks with Jenkins.
- Understand CI/CD workflows in AWS.

---

*This project is part of my hands-on DevOps and Cloud learning journey.*
