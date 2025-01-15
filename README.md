![DIAGRAM](https://github.com/user-attachments/assets/2e6d6c69-2d67-449f-b9dc-02f53d52e53b)

## CI/CD Workflow with GitHub Actions and ArgoCD

This project demonstrates a complete CI/CD pipeline setup using **GitHub Actions** for Continuous Integration (CI) and **ArgoCD** for Continuous Deployment (CD). The pipeline is triggered by pushing new code to this repository, which runs tests, builds a Docker image, uploads the image to Docker Hub, and syncs the deployment using ArgoCD based on a YAML configuration file.

### Features

- **Continuous Integration (CI)**:
  - Automatically triggered upon code push to the repository.
  - Executes unit tests on the updated code.
  - Builds a new Docker image upon successful testing.
  - Pushes the Docker image to Docker Hub.

- **Continuous Deployment (CD)**:
  - Uses ArgoCD to monitor the repository for changes.
  - Syncs the Kubernetes cluster to deploy the updated Docker image based on the YAML manifest files stored in the repository.

## Prerequisites

- Docker Hub account and repository for storing Docker images.
- Kubernetes cluster.
- ArgoCD installed and configured in the Kubernetes cluster.
- GitHub repository.
- GitHub Actions configured.

## Workflow Overview

### CI with GitHub Actions

The GitHub Actions workflow is triggered every time a new commit is pushed to the repository. The workflow does the following:

- Runs tests on the new code.
- Builds a Docker image.
- Pushes the Docker image to Docker Hub.

## CD with ArgoCD

ArgoCD is set up to monitor this repository for changes to the Kubernetes deployment configuration. When changes are detected in the YAML files, ArgoCD automatically syncs the Kubernetes cluster with the updated configuration.

### CI GitHub Actions Workflow (`ci.yml`)

Located in `.github/workflows/ci.yml`, this workflow includes the following steps:

- **Test**: Run unit tests on the pushed code.
- **Build Docker Image**: Build a new Docker image based on the code in the repository.
- **Push to Docker Hub**: Upload the built Docker image to Docker Hub.

### CD with ArgoCD

The ArgoCD setup monitors the repository for changes in the YAML file that describes the Kubernetes deployment. When a new image is pushed to Docker Hub, the following workflow happens:

- ArgoCD detects the change in the deployment configuration (e.g., the image tag).
- It triggers a sync to the Kubernetes cluster, updating the application with the new image.

### ArgoCD Application Configuration

You will need to create a new ArgoCD application that points to this repository and the `k8s/deployment.yml` file.

## How to Use

- **Set Up Docker Hub Credentials**: Add your Docker Hub credentials as GitHub secrets.
    - `DOCKER_USERNAME`: Your Docker Hub username.
    - `DOCKER_PASSWORD`: Your Docker Hub password.

- **Set Up ArgoCD**: Create an ArgoCD application as described in the ArgoCD section.

- **Push Changes**: Whenever new code is pushed to the repository, the CI pipeline runs, and a new Docker image is built and pushed to Docker Hub. ArgoCD will sync the changes automatically.

## Conclusion

This setup provides a streamlined approach to automating the testing, building, and deployment process, leveraging GitHub Actions for CI and ArgoCD for CD. By implementing this pipeline, you can ensure that your application is continuously integrated and deployed with minimal manual intervention.
