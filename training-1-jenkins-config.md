# Practice 1 - Jenkins Local Setup and Base Configuration

This practice focuses on installing and configuring Jenkins locally, preparing credentials, and enabling multibranch pipelines for the course repositories.

## Goals
- Install and run Jenkins locally with the minimum required plugins.
- Configure GitHub SSH access for Jenkins.
- Prepare multibranch pipelines for the Python and Java repositories using a dummy Jenkinsfile.
- Enable virtualized build environments using Docker so pipeline steps run in isolated containers.

## Related repositories and branches
- Python app (statement in `feat/base`): https://github.com/contreras-adr/devops-training-2025-python-app/tree/feat/base
- Java app (statement in `feat/base`): https://github.com/contreras-adr/devops-training-2025-java-app/tree/feat/base


## Prerequisites
- Docker and Docker Compose installed.
- Optional: WSL2 Ubuntu 22.04 if you are on Windows.
  - https://gist.github.com/Adhjie/8dcab8ef69a82e0b35d017725f20de19
  - https://documentation.ubuntu.com/wsl/en/latest/howto/install-ubuntu-wsl2/

## Docker Compose overview (existing `docker-compose.yml`)
The course uses an existing Docker Compose file to deploy Jenkins and a Docker-in-Docker daemon on the same network. This allows pipeline steps to run inside isolated containers while Jenkins connects to the DinD daemon over TLS.

### Network
- `devops_training_net` is a user-defined bridge network with IPAM configured on subnet `172.16.236.0/24`.
- Services attach to this network to communicate by alias/hostname.

### Volumes
- `jenkins-data`: persists Jenkins home (`/var/jenkins_home`) so configuration and jobs survive restarts.
- `jenkins-docker-certs`: stores TLS client certificates for secure Docker API access between Jenkins and DinD.

### Services
#### `dind` (Docker-in-Docker daemon)
- Image: `docker:dind`, runs privileged to enable nested Docker.
- Ports: exposes `2376` for the TLS-secured Docker API.
- Network: attached to `devops_training_net` with alias `docker` (used by Jenkins).
- Volumes:
  - `jenkins-data:/var/jenkins_home` (shared workspace if needed by pipelines).
  - `jenkins-docker-certs:/certs/client` (TLS certs).
- Environment: `DOCKER_TLS_CERTDIR=/certs` enables TLS and generates certs.

#### `jenkins`
- Built from `./jenkins/Dockerfile` for a customized Jenkins image.
- Runs as `root` and `privileged` to support Docker-based tooling inside agents.
- Ports:
  - `8080` for the Jenkins UI.
  - `50000` for inbound agents.
- Depends on `dind`, ensures Docker API is available first.
- Volumes:
  - `jenkins-data:/var/jenkins_home:rw` for Jenkins state.
  - `jenkins-docker-certs:/certs/client:ro` to read Docker TLS certs.
- Environment:
  - `DOCKER_HOST=tcp://docker:2376` points to the DinD daemon by network alias.
  - `DOCKER_CERT_PATH=/certs/client` and `DOCKER_TLS_VERIFY=1` enforce TLS.

### Start and verify
```bash
docker-compose up -d
docker-compose logs jenkins
```

Inside Jenkins (Pipeline or Script Console), verify Docker access:
```bash
docker version
```

## Configure Jenkins plugins
- Define the required plugins in `jenkins/plugins.txt` before starting Jenkins.
- The list below is the minimum required in this course to connect a Git repository and run Docker containers in pipelines:
  - `cloudbees-folder`: enables folder and multibranch organization.
  - `git`: Git SCM integration.
  - `blueocean`: visual pipeline UI for easier onboarding.
  - `docker-workflow`: Docker Pipeline steps (`docker.build`, `docker.image`, etc.).
  - `artifactory`: basic integration used in later CI/CD examples.

## Unlock Jenkins (initial setup)
When Jenkins starts for the first time, the initial password appears in the logs. In the screenshot `unlock-jenkins-1.png` you can see the output of:
```bash
docker-compose logs jenkins
```
The password value appears there, along with the path inside the container:
```
/var/jenkins_home/secrets/initialAdminPassword
```
In the "Unlock Jenkins" screen (`unlock-jenkins-2.png`), copy and paste that password into the **Administrator password** field and continue.

![Unlock Jenkins - logs](unlock-jenkins-1.png)
![Unlock Jenkins - UI](unlock-jenkins-2.png)

## Create GitHub SSH key for Jenkins
```bash
ssh-keygen -C "contreras.adr@outlook.com" -f ~/.ssh/jenkins-github
cat ~/.ssh/jenkins-github
```

## Multibranch pipelines (dummy Jenkinsfile)
Use the Jenkinsfile below as a placeholder for multibranch pipelines:
https://github.com/hoto/jenkinsfile-examples/blob/master/jenkinsfiles/001-stages-declarative-style.groovy
