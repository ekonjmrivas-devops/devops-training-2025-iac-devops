
# IAC-DEOPS Environment


## CI / CD Strategy with Jenkins and Docker



### Configure Jekins modules to install in *jenkins/plugins.txt*

### Install Jenkins in local environment.
https://github.com/jenkinsci/docker/
```bash
docker-compose up -d jenkins --build --force-recreate

```
### Install Jenkins in local environment with Podman
```bash
  cd ../../mnt/c/Users/WINDOWS_USER/PROJECT_DIR_PATH
  podman compose -f docker-compose.yml up -d jenkins

  # Get admin password from logs
  podman compose -f docker-compose.yml logs jenkins


```




### Create GitHub SSH Key for Jenkins
```bash
ssh-keygen -C "contreras.adr@outlook.com" -f ~/.ssh/jenkins-github
cat ~/.ssh/jenkins-github.
```

