# fun-with-hcl

A [Pulumi](https://www.pulumi.com/) program, written in **HCL**, that deploys a
single virtual machine on Amazon EC2. It was scaffolded from the `vm-aws-hcl`
template (`pulumi new vm-aws-hcl`).

## What it deploys

The program (`main.tf`) provisions a minimal, self-contained network and a web
server:

- A VPC (`10.0.0.0/16`) with an internet gateway
- A public subnet (`10.0.1.0/24`) that auto-assigns public IPs, plus a route
  table wiring it to the internet
- A security group allowing inbound HTTP and all outbound traffic
- A `t3.micro` EC2 instance running the latest Amazon Linux 2023 AMI, whose
  user-data boots a tiny Python HTTP server responding with
  `Hello, World from Pulumi!`

It exports the instance's `ip`, `hostname`, and `url`.

## Configuration

Configurable via stack config (see `Pulumi.dev.yaml`):

| Key                | Default       | Description                       |
| ------------------ | ------------- | --------------------------------- |
| `instance_type`    | `t3.micro`    | EC2 instance type                 |
| `vpc_network_cidr` | `10.0.0.0/16` | CIDR block for the VPC            |
| `service_port`     | `80`          | HTTP port exposed on the instance |
| `aws:region`       | `us-west-2`   | AWS region to deploy into         |

## Credentials

AWS credentials are supplied at deploy time by a [Pulumi ESC](https://www.pulumi.com/docs/esc/)
environment, referenced under `environment` in
`Pulumi.dev.yaml`. The environment logs into AWS via OIDC and exports temporary
`AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, and `AWS_SESSION_TOKEN` variables, so no
static credentials are stored in this project.

## Usage

```bash
# Preview and deploy
pulumi up

# Visit the exported URL
pulumi stack output url

# Tear it all down
pulumi destroy
```
