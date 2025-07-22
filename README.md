# GITHUB-ACTION-PRESENTATION

## CREATE AN AIM user programatic access

Give user permissions for 

AmazonEC2ContainerRegistryFullAccess

AmazonECS_FullAccess

<img width="1842" height="868" alt="image" src="https://github.com/user-attachments/assets/c1da053f-cdd8-42b2-8592-cadfc2f886b5" />

Retrive AWS Account ID and region

<img width="1898" height="527" alt="image" src="https://github.com/user-attachments/assets/cf13bcd5-c650-40dc-bef6-d109d6edb86d" />

Still on AWS create ECR repositories 

<img width="1882" height="506" alt="image" src="https://github.com/user-attachments/assets/8a22574e-f983-4e13-b906-f10842278c96" />

## Set Up GitHub Secrets

Under your GitHub repo → Settings → Secrets and variables → Actions:

<img width="1430" height="852" alt="image" src="https://github.com/user-attachments/assets/7f12efb1-5d8f-4f31-bc4d-3af26c9c2caa" />

# Docker setup

Create a folder named  app for backend  and webapp for frontend 

 In both folders add the Docker file in each folder 

 # GITHUB ACTIONWORK FLOW FILE

  Create a folder .github inside the folder create a nother folder called workflows . Inside the folder,create a fle called deploy.yml

   The jobs defined in the file include both backend named as app and frontend jobs named as webap.

    The jobs which are defined by the code include 

 1. Checkout repo

2. Cache node modules

3. Configure AWS credentials

3. Login to ECR

4. Build Docker image with Buildx caching

5. Push image to ECR

6. Deploy to ECS (via aws ecs update-service

7. The code below was used for the whole process defining each job abd step


 < name: Build, Push, and Deploy to AWS ECS with Caching

on:
  push:
    branches: [main]

jobs:
  backend:
    name: Build, Push Backend Image and Deploy to ECS
    runs-on: ubuntu-latest
    env:
      AWS_REGION: ${{ secrets.AWS_REGION }}
      AWS_ACCOUNT_ID: ${{ secrets.AWS_ACCOUNT_ID }}
      ECR_REPOSITORY_BACKEND: ${{ secrets.ECR_REPOSITORY_BACKEND }}

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3

      - name: Cache Docker layers (backend)
        uses: actions/cache@v3
        with:
          path: /tmp/.buildx-cache-app
          key: app-buildx-${{ github.sha }}
          restore-keys: |
            app-buildx-

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ${{ secrets.AWS_REGION }}

      - name: Login to Amazon ECR
        run: |
          aws ecr get-login-password --region $AWS_REGION | \
          docker login --username AWS \
          --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com

      - name: Build and push backend Docker image
        uses: docker/build-push-action@v5
        with:
          context: ./app
          push: true
          tags: ${{ secrets.AWS_ACCOUNT_ID }}.dkr.ecr.${{ secrets.AWS_REGION }}.amazonaws.com/${{ secrets.ECR_REPOSITORY_BACKEND }}:latest
          cache-from: type=local,src=/tmp/.buildx-cache-app
          cache-to: type=local,dest=/tmp/.buildx-cache-app

      - name: Deploy backend to ECS
        run: |
          aws ecs update-service \
            --cluster your-backend-cluster \
            --service your-backend-service \
            --force-new-deployment \
            --region $AWS_REGION

  frontend:
    name: Build, Push Frontend Image and Deploy to ECS
    runs-on: ubuntu-latest
    env:
      AWS_REGION: ${{ secrets.AWS_REGION }}
      AWS_ACCOUNT_ID: ${{ secrets.AWS_ACCOUNT_ID }}
      ECR_REPOSITORY_FRONTEND: ${{ secrets.ECR_REPOSITORY_FRONTEND }}

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3

      - name: Cache Docker layers (frontend)
        uses: actions/cache@v3
        with:
          path: /tmp/.buildx-cache-webapp
          key: webapp-buildx-${{ github.sha }}
          restore-keys: |
            webapp-buildx-

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ${{ secrets.AWS_REGION }}

      - name: Login to Amazon ECR
        run: |
          aws ecr get-login-password --region $AWS_REGION | \
          docker login --username AWS \
          --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com

      - name: Build and push frontend Docker image
        uses: docker/build-push-action@v5
        with:
          context: ./webapp
          push: true
          tags: ${{ secrets.AWS_ACCOUNT_ID }}.dkr.ecr.${{ secrets.AWS_REGION }}.amazonaws.com/${{ secrets.ECR_REPOSITORY_FRONTEND }}:latest
          cache-from: type=local,src=/tmp/.buildx-cache-webapp
          cache-to: type=local,dest=/tmp/.buildx-cache-webapp

      - name: Deploy frontend to ECS
        run: |
          aws ecs update-service \
            --cluster your-frontend-cluster \
            --service your-frontend-service \
            --force-new-deployment \
            --region $AWS_REGION >


            Now you can push to gitbub main

            

            Once you run the code push to main branch 



