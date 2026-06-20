# AWS Infrastructure

Multi-AZ AWS infrastructure with automated failover, Grafana monitoring, and Zero Trust networking. Deployed via Terraform.

## How it works

The infrastructure spans 2 availability zones (eu-central-1a and eu-central-1b) with strict public/private subnet separation. An Application Load Balancer distributes traffic across two ARM-based EC2 webservers in private subnets. A PostgreSQL RDS instance runs in Multi-AZ mode with automatic failover in under 60 seconds.

All webservers and databases sit in private subnets with no public IPs. Access to internal resources requires OpenVPN. A self-hosted Prometheus and Grafana stack provides real-time metrics, alerting on CPU, memory, and HTTP error rates.

Infrastructure as Code via Terraform deploys the full stack in 15 minutes. NAT instances replace NAT Gateway to reduce costs by approximately 82%.

## Architecture

![Architecture](images/architecture.png)

## Tech stack

| Component | Technology |
|-----------|------------|
| Infrastructure | Terraform |
| Compute | EC2 t4g.micro (ARM) |
| Database | RDS PostgreSQL Multi-AZ |
| Load balancer | Application Load Balancer |
| Monitoring | Prometheus + Grafana + Loki |
| VPN | OpenVPN |
| DNS | Route53 private hosted zone |

## Setup

```bash
cd terraform
terraform init
terraform apply -auto-approve
```

Connect to VPN:

```bash
scp -i mehdi-key.pem ubuntu@<vpn-public-ip>:/home/ubuntu/client.ovpn .
sudo openvpn --config client.ovpn
```

Requires AWS credentials and an SSH key pair (`mehdi-key`) in eu-central-1.
