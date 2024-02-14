# Additional packages, installed on the hosts

Installing and configuring required packages on the VMs.

## Docker
please visit this link on [how to install docker on debian](https://docs.docker.com/engine/install/debian/).

### required permissions

```bash
# Create the docker group if it does not exist
sudo groupadd docker

# add current user to the group
sudo usermod -aG docker $USER

# add gitlab-runner user to the group
sudo usermod -aG docker gitlab-runner
```

## Docker-compose

```bash
# Check the current release and, if necessary, update it in the command that follows:
sudo curl -L https://github.com/docker/compose/releases/download/1.25.3/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose

# Next we’ll set the permissions:
sudo chmod +x /usr/local/bin/docker-compose

# Then we’ll verify that the installation was successful by checking the version:
docker-compose --version
```
