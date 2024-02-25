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

The following use cases/events were considered:
- A Pull Request is opened, edited, synchronize, reopened or closed.
- A commit is pushed to a Pull Request.
- A Pull Request is merged to the main branch.
- A new release is published.

The workflow file is located at [.github/workflows/ci-cd.yml](.github/workflows/ci-cd.yml).

The workflow is divided into the following jobs:

![workflows.png](src%2Fmain%2Fresources%2Fworkflows.png)

#### Prepare

![prepare.png](src%2Fmain%2Fresources%2Fprepare.png)

#### Build and test

![build-and-test.png](src%2Fmain%2Fresources%2Fbuild-and-test.png)

#### Build image and push

![build-image-and-push.png](src%2Fmain%2Fresources%2Fbuild-image-push.png)
