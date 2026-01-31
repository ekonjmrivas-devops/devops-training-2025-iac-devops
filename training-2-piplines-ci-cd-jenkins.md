# Practice 2 - Jenkins CI/CD Pipelines with Docker and Groovy

In this practice you will build CI/CD pipelines in Jenkins, implement Jenkinsfile logic with Groovy, and use Docker as the virtualization layer for pipeline steps. The work is done against the Python and Java project repositories.

## Related repositories and branches
- Python app (statement in `feat/base`): https://github.com/contreras-adr/devops-training-2025-python-app/tree/feat/base
- Java app (statement in `feat/base`): https://github.com/contreras-adr/devops-training-2025-java-app/tree/feat/base

## Prerequisites
- Jenkins installed locally with the minimum plugins.
- Both project repositories have a multibranch pipeline created in Jenkins and connected to a dummy `devops/jenkinsfile`.

## Objectives
- Learn how to structure Jenkins pipelines and attach training documentation to the repository.
- Use Docker containers as the execution agent for pipeline steps.
- Learn Groovy basics and how to create Groovy libraries to reuse code.

## Python pipeline requirements
Create a Jenkins pipeline for the Python project with these stages:

### Stage: CI
- Run unit tests inside a container using `python:3.6-slim`.
- Build the Docker image (use local image cache).

### Stage: CD
- Build the Docker image (use local image cache) if not already built in CI.
- Start the database and app on the Jenkins agent using Docker Compose (note that no external environments exist yet).
- Run end-to-end tests by calling the API endpoint exposed by the app.
- Clean up with `docker compose down --volume`.

## Java pipeline requirements
Create a Jenkins pipeline for the Java project with these stages:

### Stage: CI
- Run lint via the Makefile inside a container using `maven:3.8.6-openjdk-11-slim`.
  - The Makefile is a simple task runner that groups commands. Run tasks with `make <target>` (for example, `make lint`).
- Run unit tests via the Makefile inside a container using `maven:3.8.6-openjdk-11-slim`.
- Build the Docker image (use local image cache).

### Stage: CD
- Start the database and app on the Jenkins agent using Docker Compose (note that no external environments exist yet).
- Run end-to-end tests by calling the API endpoint exposed by the app.
- Clean up with `docker compose down --volume`.

## Deliverables
- Jenkinsfiles for both repositories implementing the CI/CD stages above.
- Minimal Groovy shared library or helper function(s) used across stages (documented in the Jenkinsfile).
- Short README updates in each project repository explaining how to run the pipelines and what each stage does.
