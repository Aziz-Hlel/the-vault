---
tags:
  - cloud
  - how-to
  - vps
  - bash
  - best-practices
---

---


## Update system upon first login

**Warning :** Amazon Linux does not use the `apt` package manager found in Debian/Ubuntu systems. Instead, Amazon Linux uses **`dnf`**, so replace them in the code if you're on Amazon Linux based os

``` bash
sudo dnf update
sudo dnf upgrade
```


## Do not skip this is you're not on AWS

-  By default, Aws automatically creates the  default non-root user with  `sudo` privileges when you launch an instance (**`ec2-user`** for Amazon linux and **`debian`** for Debian ),direct `root` SSH Login is Disabled and Password Authentication is Disabled, if you not using aws as cloud provider and ec2 as vps service please ensure to make all these details.
- you can check https://www.youtube.com/watch?v=Q1Y_g0wMwww for those steps



## Set System Time zone to UTC

- ``` bash
    sudo timedatectl set-timezone UTC
    ```


## Install Fail2ban

- Automatically bans IP addresses that show malicious login signs (such as repeated failed SSH and others login that do not concern you )
- By default, installing Fail2ban enables the **SSH protection jail** 

-   ```Bash
    sudo apt install fail2ban -y
    sudo systemctl enable fail2ban
    ```


## Install and Set up swap

-  [[Set up swap]]



### Kernel Network Hardening

Protect against [[SYN floods]] and [[IP spoofing]] :

- **Step 1 :** Open the sysctl file
  ```bash
  sudo nano /etc/sysctl.conf
  ```

- **Step 2 :** Add these settings at the bottom of the file 

```Ini, TOML
# Protect against SYN flood attacks
net.ipv4.tcp_syncookies = 1

# Protect against IP spoofing (Reverse Path Filtering)
net.ipv4.conf.all.rp_filter = 1
net.ipv4.conf.default.rp_filter = 1
```

- **Step 3 : Apply  with `sudo systemctl restart systemd-journald`.


### Cap Systemd Journal Logs

- Cloud instances frequently crash because unmanaged system or application logs quietly fill 100% of the disk, to prevent that limit `journald` storage 
  
-  **Step 1 :** Open the journald file
  ```bash
  sudo nano /etc/systemd/journald.conf
  ```

- **Step 2 :** Add these settings at the bottom of the file 

- ```Ini, TOML
	# Cap maximum storage size
	SystemMaxUse=500M
	# Automatically delete logs older than a specific timeframe regardless of size
	MaxRetentionSec=1month
```
-  Apply with `sudo systemctl restart systemd-journald`.



### Configure unattended-upgrades


- ! The `unattended-upgrades` package is unique to Debian and Ubuntu systems. Amazon Linux uses **`dnf-automatic`** instead to handle automated background security updates, you can find the steps below

#### Unattended-upgrades
- #### Step 1: Install and enable `unattended-upgrades`

```Bash

#Most Debian/Ubuntu servers have it installed by default, but you can ensure it is present with
sudo apt update && sudo apt install unattended-upgrades -y
#Make sure the service is enabled
sudo dpkg-reconfigure --priority=low unattended-upgrades
#(Select **Yes** when prompted to enable automatic updates)._

```

- #### Step 2: Configure Automatic Reboots and Time
	Open the main configuration file in a text editor:
	
- ```Bash
	sudo nano /etc/apt/apt.conf.d/50unattended-upgrades
	```
	
	Scroll down to the reboot settings section or add the following lines at the bottom of the file:


```Ini, TOML
	// Automatically reboot WITHOUT CONFIRMATION if a reboot is required
	Unattended-Upgrade::Automatic-Reboot "true";
	
	// Set a specific reboot time (e.g., 03:00 AM) to minimize disruption
	// ! Carefull, you need to make sure 
	Unattended-Upgrade::Automatic-Reboot-Time "03:00";
	
	// (Optional) Delay reboot if logged-in SSH users are active
	Unattended-Upgrade::Automatic-Reboot-WithUsers "false";
```

- #### Step 3: Test the Configuration

	Verify that there are no syntax errors in your APT configuration:

```Bash
sudo unattended-upgrade --dry-run --debug
```
	If it parses cleanly without throwing errors, your automatic updates and scheduled reboots are active and ready.


#### dnf-automatic
- ! didn't work abandoned, recommended by ai to do it with Aws SSM, too much headache  
- #### Step 1: Install and enable `dnf-automatic`
- ```bash
  sudo dnf install dnf-automatic -y
  sudo nano /etc/dnf/automatic.conf
  ```


### Configure Docker Log Limits

- #### Install, start and enable Docker and add default user to permission

```Bash
sudo dnf update -y # For Amazon Linux 2023 or replace all dnf with apt
sudo dnf install docker -y   
sudo systemctl start docker
sudo systemctl enable docker

#Add your default user (`ec2-user` in the case of Amazon linux) to the `docker` group so you can execute Docker commands without using `sudo`.
sudo usermod -aG docker ec2-user
```

By default, log files grow indefinitely. Create or edit `/etc/docker/daemon.json`:

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