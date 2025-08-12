# Barista Cafe - ECS Web Application Deployment

[![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)](https://aws.amazon.com/)
[![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)](https://www.docker.com/)
[![GitHub Actions](https://img.shields.io/badge/github%20actions-%232671E5.svg?style=for-the-badge&logo=githubactions&logoColor=white)](https://github.com/features/actions)
[![Nginx](https://img.shields.io/badge/nginx-%23009639.svg?style=for-the-badge&logo=nginx&logoColor=white)](https://nginx.org/)
[![HTML5](https://img.shields.io/badge/html5-%23E34F26.svg?style=for-the-badge&logo=html5&logoColor=white)](https://developer.mozilla.org/en-US/docs/Web/HTML)
[![Bootstrap](https://img.shields.io/badge/bootstrap-%238511FA.svg?style=for-the-badge&logo=bootstrap&logoColor=white)](https://getbootstrap.com/)
[![Trivy](https://img.shields.io/badge/trivy-1904DA?style=for-the-badge&logo=trivy&logoColor=white)](https://trivy.dev/)

A modern DevOps implementation for deploying a static cafe website using AWS ECS, featuring automated CI/CD pipelines, security scanning, and infrastructure as code.

## 🏗️ Architecture Overview

```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   GitHub Repo   │───▶│  GitHub Actions  │───▶│   AWS ECR       │
│                 │    │                  │    │                 │
└─────────────────┘    └──────────────────┘    └─────────────────┘
                                │                        │
                                ▼                        │
                       ┌──────────────────┐              │
                       │ Security Scanning│              │
                       │    (Trivy)       │              │
                       └──────────────────┘              │
                                │                        │
                                ▼                        │
                       ┌──────────────────┐              │
                       │   ECS Cluster    │◀─────────────┘
                       │ (EC2 / Fargate)  │
                       └──────────────────┘
```

## 🚀 Features

### DevOps Pipeline
- **Multi-stage CI/CD**: Build → Security → Deploy
- **Automated Docker builds** with commit-based tagging
- **Vulnerability scanning** with Trivy integration
- **Zero-downtime deployments** to AWS ECS
- **Artifact management** for cross-job data sharing

### Security
- **Container image scanning** before deployment
- **SARIF integration** with GitHub Security tab
- **AWS IAM** role-based access control
- **Secrets management** via GitHub Secrets

### Infrastructure
- **AWS ECS** with EC2 and Fargate launch types
- **Amazon ECR** for container registry
- **GitHub Container Registry** for public container images
- **CloudWatch Logs** for centralized logging
- **Resource optimization** (128 CPU, 256MB memory)

## 📋 Prerequisites

### AWS Resources
- ECS Cluster (EC2 or Fargate launch type)
- ECR Repository
- ECS Service
- IAM Role with ECS/ECR permissions
- EC2 instances with ECS agent (for EC2 launch type)
- VPC with subnets (for Fargate launch type)

### GitHub Secrets
```bash
AWS_ACCESS_KEY_ID       # AWS access key
AWS_SECRET_ACCESS_KEY   # AWS secret key
AWS_REGION             # AWS region (e.g., us-east-2)
ECR_REPOSITORY_URI     # ECR repository URI
CONTAINER_NAME         # Container name in task definition
ECS_SERVICE            # ECS service name
ECS_CLUSTER            # ECS cluster name
```

## 🛠️ Technology Stack

| Component | Technology | Purpose |
|-----------|------------|----------|
| **Frontend** | HTML5, CSS3, Bootstrap 5 | Static website |
| **Web Server** | Nginx Alpine | Lightweight HTTP server |
| **Containerization** | Docker | Application packaging |
| **Container Registry** | Amazon ECR | Image storage |
| **Orchestration** | Amazon ECS | Container management |
| **CI/CD** | GitHub Actions | Automated pipeline |
| **Security Scanning** | Trivy | Vulnerability assessment |
| **Monitoring** | CloudWatch | Logging and metrics |

## 🔄 CI/CD Pipeline

### ECS Deployment Pipeline
1. **Build Stage**: Build and push to ECR with commit SHA tag
2. **Security Stage**: Trivy vulnerability scanning
3. **Deploy Stage**: Deploy to ECS service

### GHCR Pipeline
1. **Build Stage**: Build and push to GitHub Container Registry
2. **Tagging**: Multiple tags (branch, SHA, latest)
3. **Security Stage**: Trivy vulnerability scanning
4. **Metadata**: Extract and apply Docker labels

## 📁 Project Structure

```
ecs-webapp-deployment/
├── .github/
│   └── workflows/
│       ├── ecs-deploy.yaml      # ECS deployment caller
│       ├── ghcr-workflow.yaml   # Reusable ECS workflow
│       └── ghcr-push.yaml       # GHCR push workflow
├── css/                         # Stylesheets
├── js/                          # JavaScript files
├── images/                      # Static assets
├── Dockerfile                   # Container definition
├── task-definition.json         # ECS task configuration
├── index.html                   # Main webpage
└── README.md                    # Project documentation
```

## 🚀 Deployment

### Automatic Deployment
Push to `main` branch triggers the pipeline:
```bash
git add .
git commit -m "Deploy new features"
git push origin main
```

### Manual Deployment
1. Navigate to **Actions** tab in GitHub
2. Select **Build and Deploy to ECS** workflow
3. Click **Run workflow**

## 📊 Resource Configuration

### EC2 Launch Type
```json
{
  "cpu": "128",
  "memory": "256",
  "networkMode": "bridge",
  "requiresCompatibilities": ["EC2"]
}
```

### Fargate Launch Type
```json
{
  "cpu": "256",
  "memory": "512",
  "networkMode": "awsvpc",
  "requiresCompatibilities": ["FARGATE"],
  "executionRoleArn": "arn:aws:iam::ACCOUNT:role/ecsTaskExecutionRole"
}
```

### Container Specs
- **CPU**: 128-256 units (0.125-0.25 vCPU)
- **Memory**: 256-512 MB
- **Port**: 80 (HTTP)
- **Base Image**: nginx:alpine

## 🔍 Monitoring & Troubleshooting

### CloudWatch Logs
```bash
# Log group: /ecs/barista-cafe-app-task-definition
# Stream prefix: ecs
```

### Common Issues
| Issue | Solution |
|-------|----------|
| Task placement failure (EC2) | Check EC2 instance capacity |
| Task placement failure (Fargate) | Verify subnet configuration and availability |
| Image pull errors | Verify ECR permissions |
| Service instability | Review task definition resources |
| Pipeline failures | Check GitHub Secrets configuration |
| Fargate networking issues | Ensure VPC, subnets, and security groups are configured |

## 🔐 Security Best Practices

- ✅ **Vulnerability scanning** on every build
- ✅ **Least privilege** IAM roles
- ✅ **Secrets management** via GitHub Secrets
- ✅ **Container image** signed and scanned
- ✅ **Network isolation** with VPC
- ✅ **Logging enabled** for audit trails

## 📈 Performance Optimization

- **Nginx Alpine**: Minimal base image (~5MB)
- **Static content**: No server-side processing
- **Resource limits**: Optimized for cost efficiency
- **Caching**: Browser and CDN friendly

## 🤝 Contributing

1. Fork the repository
2. Create feature branch (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`)
5. Open Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙋‍♂️ Support

For support and questions:
- 📧 Email: hello@barista.co
- 📱 Phone: (65) 305 2409 671
- 🐛 Issues: [GitHub Issues](https://github.com/your-username/ecs-webapp-deployment/issues)

---

**Built with ❤️ for modern DevOps practices**
