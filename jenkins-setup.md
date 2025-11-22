# Jenkins Setup Instructions

## Access Jenkins
- **URL**: http://localhost:8081
- **Initial Admin Password**: `85545ec2c9b5436fbf356132e57cf2b7`

## Setup Steps:
1. Open http://localhost:8081 in browser
2. Enter the admin password above
3. Install suggested plugins
4. Create admin user (or skip and use admin)
5. Set Jenkins URL as http://localhost:8081

## Create Pipeline:
1. Click "New Item"
2. Enter name: "two-tier-devops-pipeline"
3. Select "Pipeline" 
4. In Pipeline section, select "Pipeline script from SCM"
5. SCM: Git
6. Repository URL: Your git repository URL
7. Script Path: Jenkinsfile
8. Save and Build

## Docker Hub Credentials:
1. Go to Manage Jenkins > Credentials
2. Add new Username/Password credential
3. ID: `dockerhub-creds`
4. Username: Your Docker Hub username
5. Password: Your Docker Hub password

## Pipeline Stages:
- ✅ Checkout
- ✅ Build Images (Parallel)
- ✅ Test
- ✅ Push Images
- ✅ Deploy
- ✅ Cleanup