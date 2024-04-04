# Spring Boot application with tests and CI/CD workflow

The main purpose of this repository is to demonstrate how to set up a CI/CD workflow using GitHub Actions.

The application used is a simple Spring Boot application with a REST controller and a test class.

## Workflows

There are two workflows in this repository:
- [Validate PR](#validate-pr)
- [CI-CD](#ci-cd)

### Validate PR

This workflow validates the pull request title. 

It uses an action from [amannn/action-semantic-pull-request](https://github.com/amannn/action-semantic-pull-request) to check if the pull request title follows the semantic versioning pattern.

The workflow is triggered when a pull request is opened, edited or synchronized.

The workflow file is located at [.github/workflows/validate-pr.yml`](.github/workflows/validate-pr.yml).

### CI-CD

This workflow is responsible for:
 - Building and testing the application.
 - Building a Docker image and pushing it to Docker Hub.
 - Deploying the application to an environment.

The workflow is triggered when one of the following events occurs:
- A Pull Request is opened, synchronized, reopened or closed.
- A commit is pushed to a Pull Request.
- A Pull Request is merged to the branch `main`.
- A new release is published.

The workflow file is located at [.github/workflows/ci-cd.yml](.github/workflows/ci-cd.yml).

The workflow is divided into the following jobs:

![workflows.png](src%2Fmain%2Fresources%2Fworkflows.png)

#### Prepare

![prepare.png](src%2Fmain%2Fresources%2Fprepare.png)

#### Build and test

![build-and-test.png](src%2Fmain%2Fresources%2Fbuild-and-test.png)

The code coverage is executed with JaCoCo maven plugin and the report is generated with [JaCoCo Report](https://github.com/marketplace/actions/jacoco-report).

#### Build image and push

![build-image-and-push.png](src%2Fmain%2Fresources%2Fbuild-image-push.png)

#### Deploy

The application is deployed in an AWS EC2 instance using SSH.

In order to authenticate the SSH connection, a private key is needed and so, it was created in AWS and stored as a secret in the repository.
The secrets used in the workflow are:
- `DOCKER_USERNAME` and `DOCKER_PASSWORD` to authenticate in Docker Hub.
- `AWS_EC2_USERNAME`, `AWS_EC2_IPADDRESS` and `AWS_EC2_PRIVATE_KEY` to connect to the EC2 instance.

__Note:__ Each time the instance is stopped and started, the EC2 instance public IP address changes and we need to update the `AWS_EC2_IPADDRESS` secret. To avoid this, an Elastic IP could have been used but for now, it was not implemented.

The deploy job has the following steps:
- Login to Docker Hub.
- Sets permissions to the private key file that is going to be used to authenticate SSH connection.
- The following steps are then executed inside the EC2 instance (connected via SSH):
  - Pull the new image.
  - Stop the running container (by container name).
  - Remove the container (by container name).
  - Run the new container.

Containers are named with the following pattern: `spring-boot-tests-cicd-<environment>`.

![containers.png](src%2Fmain%2Fresources%2Fcontainers.png)

The environment variable value is added to the container as an environment variable in the `run` command. It can be `STAGING` or `PRODUCTION`.

The application will load the environment variable and use the value in the `/api/greeting` endpoint to present a message with the environment name.

![hello-stag.png](src%2Fmain%2Fresources%2Fhello-stag.png)
![hello-prod.png](src%2Fmain%2Fresources%2Fhello-prod.png)

The application port is set to 8080 and the container port is mapped to the host port 8080 for the STAGING environment and 8081 for the PRODUCTION environment.
