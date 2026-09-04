---
tags:
  - infra/docker
  - how-to
  - vps
---

---

- #### Install, start and enable Docker and add default user to permission

```Bash
sudo dnf update -y # For Amazon Linux 2023 or replace all dnf with apt
sudo dnf install docker -y   
sudo systemctl start docker
sudo systemctl enable docker

#Add your default user (`ec2-user` in the case of Amazon linux) to the `docker` group so you can execute Docker commands without using `sudo`.
sudo usermod -aG docker ec2-user
```


- #### Configure Docker Log Limits

```JSON
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  }
}
```

Restart Docker with `sudo systemctl restart docker`.

- #### install and enable Docker compose

```Bash
#Create the plugin directory
sudo mkdir -p /usr/libexec/docker/cli-plugins
#Download Docker Compose V2
sudo curl -SL "https://github.com/docker/compose/releases/latest/download/docker-compose-linux-$(uname -m)" -o /usr/libexec/docker/cli-plugins/docker-compose
#Apply executable permissions
sudo chmod +x /usr/libexec/docker/cli-plugins/docker-compose
#Verify the installation
docker compose version
```

- #### Alias Option (Optional but recommended)
	[[replace docker-compose with docker compose when excuting]]
