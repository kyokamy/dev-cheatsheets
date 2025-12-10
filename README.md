# 📚 Developer Cheatsheets Collection

### *Your one-stop shop for all the commands you'll forget 5 minutes after Googling them*

A comprehensive collection of useful commands, guidelines, and cheatsheets for various development tools and technologies. Because life's too short to Google the same command 47 times.

---

## 📋 Table of Contents

- [Node.js](#-nodejs)
- [Kubernetes](#-kubernetes)
- [Terraform](#-terraform)
- [Docker](#-docker)
- [Git](#-git)
- [Bash & Linux](#-bash--linux)
- [AWS CLI](#-aws-cli)

---

## 🚀 Node.js

**Location:** [`nodejs/nodejs-website-cheatsheet.md`](./nodejs/nodejs-website-cheatsheet.md)

Complete guide to building Node.js websites, covering:
- Project setup and initialization
- Package management (npm)
- Framework installation (Next.js, Express, React)
- Essential packages and libraries
- Development tools and testing
- Build and deployment
- Debugging and troubleshooting
- Performance optimization

**Quick Start:**
```bash
npm init -y
npm install next react react-dom
npm run dev
```

---

## ☸️ Kubernetes

**Location:** [`kubernetes/kubernetes-cheatsheet.md`](./kubernetes/kubernetes-cheatsheet.md)

Master Kubernetes with this comprehensive guide:
- Installation and cluster setup
- Pod management
- Deployments and scaling
- Services and networking
- ConfigMaps and Secrets
- Persistent volumes
- Namespaces
- Debugging and troubleshooting
- Advanced operations (Jobs, StatefulSets, DaemonSets, Ingress)

**Quick Start:**
```bash
kubectl get pods
kubectl create deployment nginx --image=nginx:latest
kubectl expose deployment nginx --port=80 --type=LoadBalancer
```

---

## 🏗️ Terraform

**Location:** [`terraform/terraform-cheatsheet.md`](./terraform/terraform-cheatsheet.md)

Infrastructure as Code made simple:
- Installation and setup
- Configuration files and structure
- Variables and outputs
- Resources and data sources
- State management
- Workspaces
- Modules
- Functions and expressions
- Advanced features

**Quick Start:**
```bash
terraform init
terraform plan
terraform apply
```

---

## 🐳 Docker

**Location:** [`docker/docker-cheatsheet.md`](./docker/docker-cheatsheet.md)

Master containerization with this comprehensive guide:
- Image management and building
- Container lifecycle operations
- Networking and port mapping
- Volumes and persistent storage
- Docker Compose orchestration
- Multi-stage builds
- Health checks and resource limits
- Debugging and troubleshooting
- Cleanup and optimization

**Quick Start:**
```bash
docker build -t myapp:latest .
docker run -d -p 8080:80 myapp:latest
docker-compose up -d
```

---

## 🔀 Git

**Location:** [`git/git-cheatsheet.md`](./git/git-cheatsheet.md)

Version control made simple:
- Installation and configuration
- Basic workflow (add, commit, push)
- Branch management and merging
- Remote operations
- Tags and releases
- History and logs
- Undoing changes
- Stashing
- Advanced operations (rebase, cherry-pick, submodules)

**Quick Start:**
```bash
git init
git add .
git commit -m "Initial commit"
git push -u origin main
```

---

## 🐧 Bash & Linux

**Location:** [`bash/bash-cheatsheet.md`](./bash/bash-cheatsheet.md)

Essential Linux and Bash commands for daily operations:
- File and directory operations
- Text processing (grep, sed, awk)
- Permissions and ownership
- Process management
- Redirection and pipes
- Compression and archives
- Network commands
- Advanced Bash features
- Useful one-liners

**Quick Start:**
```bash
ls -la
grep "pattern" file
find . -name "*.txt"
ps aux | grep process
```

---

## ☁️ AWS CLI

**Location:** [`aws/aws-cli-cheatsheet.md`](./aws/aws-cli-cheatsheet.md)

Master AWS from the command line:
- Installation and configuration
- EC2 instance management
- S3 bucket and file operations
- IAM user and policy management
- VPC and networking
- Lambda functions
- CloudFormation stacks
- CloudWatch, Route 53, RDS, ECS, ECR
- Query and output formatting
- Useful one-liners

**Quick Start:**
```bash
aws configure
aws ec2 describe-instances
aws s3 ls
aws lambda list-functions
```

---

## 🤝 Contributing

Found a typo? Have a useful command to add? Want to create a new cheatsheet?

1. Fork this repository
2. Create your feature branch (`git checkout -b feature/amazing-cheatsheet`)
3. Commit your changes (`git commit -m 'Add some amazing commands'`)
4. Push to the branch (`git push origin feature/amazing-cheatsheet`)
5. Open a Pull Request

---

## 📝 Format Guidelines

When adding new cheatsheets, please follow this structure:

1. **Title** - Clear and descriptive
2. **Subtitle** - Fun, memorable tagline
3. **Sections** - Organized by topic/use case
4. **Code blocks** - All commands in proper code blocks
5. **Comments** - Helpful inline comments
6. **Quick Reference** - Table at the end
7. **Emergency Commands** - For when everything breaks

---

## 📄 License

This project is open source and available under the [MIT License](LICENSE).

---

## ⭐ Star History

If you find this useful, please consider giving it a star! ⭐

---

## 🙏 Acknowledgments

- All the developers who've Googled the same commands repeatedly
- Stack Overflow (for the answers, not the dates)
- The open-source community

---

**Made with ☕ and the frustration of forgetting commands**

*Last updated: When you needed that command you swore you'd remember*

