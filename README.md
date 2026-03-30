# HTTPS Demo — Cloud Engineering Challenge

A small containerized web service that runs locally over HTTPS using nginx and Docker.

## How to run

### Requirements
- Docker container installed (Docker desktop is one)
- openssl or through GitBash on Windows

### Step 1 - Generate the SSL certificate 
```bash
openssl req -x509 -nodes -days 365 -newkey rsa:2048  -keyout key.pem -out cert.pem -subj "//CN=localhost"
```

### Step 2 - To Start
```bash
docker compose up --build
```

Visit: https://localhost:8443
Self signed certs are expected locally so ignore the browser warning. (click advanced, then proceed)

### Step 3 - To Stop
```bash
docker compose down
```

## Design choices
I kept it simple on purpose, nginx handles the web serving and TLS without needing to write any application code. I also used the Alpine base image to keep the container lightweight, the port is mapped to 8443 instead of 443 to avoid permission issues on local machines.

## What I would improve with more time
- Structure JSON logging from nginx to stdout
- Create a local CA rather than a self signed cert
- Write a Makefile to simplify commands during set up

## How I would deploy this to AWS
The way I'd approach this in production is pushing the image to ECR, running it on ECS Fargate, and putting an Application Load Balancer in front of it. The ALB would handle the real HTTPS certificate through ACM so you wouldn't need to manage certs manually. Route 53 would handle DNS.

## Why storing a private SSL key in a repository is dangerous
The key.pem file is basically the password to your certificate. If someone gets it they can pretend to be your server or read encrypted traffic. Even if you delete it later, it stays in Git history forever. In a real project this would go in something like AWS Secrets Manager and get pulled in at runtime, never committed to code.

## AI tools used
I used Claude to better understand how to implement git and nginx in this challenge. I also used it to explain how to format a readme file and explain how to access