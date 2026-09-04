---
tags:
  - cloud/aws/ec2
  - how-to
  - best-practices
---

---

- Basically security group is a the Aws Firewall service, instead of setting up your firewall within your ec2 instance with ufw (uncomplicated firewall) , you do it outside, a cool extra security measure
- Security group has 2 configurations : Inbound rules, controlling the incoming traffic to your server and outbound rules only govern traffic that originates _from_ your EC2 instance going outward
- For outbound rules allowing **All traffic** to **0.0.0.0/0** (or IPv6 `::/0`) the **default and industry-standard configuration** for an EC2 instance serving web applications.
  Your web application usually requires unrestricted outbound access for several operational needs:
	- Installing OS updates and software packages (via `apt`, `yum`, or `dnf`).
	- Fetching application dependencies (npm, pip, composer, or Docker images).
	- Communicating with third-party APIs (payment gateways like Stripe, OAuth login providers, or external SaaS tools).
	-  Sending logs or metrics to cloud monitoring platforms.

| **Type**          | **Protocol** | **Port Range** | **Destination** | **Description**                                  |
| ----------------- | ------------ | -------------- | --------------- | ------------------------------------------------ |
| **All traffic**   | ALL          | ALL            | `0.0.0.0/0`     | Allows all outgoing IPv4 traffic                 |
| **All traffic**   | ALL          | ALL            | `::/0`          | Allows all outgoing IPv6 traffic (if using IPv6) |


- For inbound rules (No Load Balancer):

| **Type**  | **Protocol** | **Port Range** | **Source**                                                           | **Description**               |
| --------- | ------------ | -------------- | -------------------------------------------------------------------- | ----------------------------- |
| **HTTP**  | TCP          | 80             | `0.0.0.0/0`                                                          | allow traffic from http port  |
| **HTTPS** | TCP          | 443            | `0.0.0.0/0`                                                          | allow traffic from https port |
| **SSH**   | TCP          | 22             | `Your-Public-IP/32`<br>or anywhere if you don't <br>have a static ip | allow traffic from ssh port   |

- If your instance uses IPv6, add identical HTTP (80) and HTTPS (443) rules with the source set to `::/0`.