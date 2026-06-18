# Deploy Docker Application using Blue-Green Deployment on AWS ECS with Jenkins

## Step 1: Create Application Files

Create the following files:

* app.py
* requirements.txt
* Dockerfile
* Jenkinsfile

Update all files with the required code and push them to GitHub.

---

## Step 2: Create Amazon ECR Repository

Create an ECR repository:

* app-1

This repository will store Docker images.

---

## Step 3: Create ECS Infrastructure

Create:

* ECS Cluster
* ECS Task Definition

Create two Target Groups:

* app-1-blue
* app-1-green

Create an Application Load Balancer (ALB).

During ALB creation:

* Select app-1-blue as the default target group.

After the ALB is created:

* Open Listener Rules.
* Add a new rule.
* Forward requests such as `/test/*` to app-1-green target group.

---

## Step 4: Create ECS Services

Create two ECS Services:

* app-1-blue-service
* app-1-green-service

Configuration:

Blue Service:

* Target Group: app-1-blue

Green Service:

* Target Group: app-1-green

Use the same:

* Cluster
* Task Definition
* Security Group
* Subnets

---

## Step 5: Configure Jenkins

Create a Jenkins Pipeline.

Pipeline Flow:

1. Checkout Source Code from GitHub
2. Build Docker Image
3. Login to ECR
4. Tag Docker Image
5. Push Docker Image to ECR
6. Register New ECS Task Definition Revision
7. Update Green ECS Service
8. Wait for Health Checks
9. Switch ALB Traffic from Blue Target Group to Green Target Group
10. Verify Application

---

## Step 6: Access Application

Access the application using the ALB DNS Name.

Example:

http://ALB-DNS-NAME

---

## Step 7: Deploy New Version

Make changes in app.py.

Commit and push code to GitHub.

Jenkins automatically:

* Builds a new Docker image
* Pushes image to ECR
* Creates a new Task Definition revision
* Updates app-1-green-service
* Verifies health checks
* Switches production traffic to app-1-green

The previous Blue environment remains available for rollback.


app.py version 1

from flask import Flask

app = Flask(__name__)

@app.route('/')
def home():
    return """
    <h1>Blue Environment - Version 1</h1>
    <p>Application deployed successfully.</p>
    """

@app.route('/health')
def health():
    return "Healthy", 200

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)

Benefits of Blue-Green Deployment:

Zero Downtime Deployment,
Easy Rollback,
Safer Releases,
Reduced Risk,
Better User Experience

Technologies Used:

Amazon Web Services ECS,
Amazon Web Services ECR,
Jenkins,
Docker,
GitHub,
Python Flask,
Application Load Balancer (ALB)
