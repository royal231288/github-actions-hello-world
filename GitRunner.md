# GitHub Actions Runners - Complete Guide

## Table of Contents
- [Introduction to Runners](#introduction-to-runners)
- [GitHub-Hosted vs Self-Hosted Runners](#github-hosted-vs-self-hosted-runners)
- [Setting Up Self-Hosted Runner on AWS EC2](#setting-up-self-hosted-runner-on-aws-ec2)
- [Managing Self-Hosted Runners](#managing-self-hosted-runners)
- [Best Practices and Security](#best-practices-and-security)
- [Troubleshooting](#troubleshooting)

---

## Introduction to Runners

### What is a Runner?

A **runner** is a server (physical or virtual machine) that executes your GitHub Actions workflows. When you trigger a workflow, GitHub sends the jobs to available runners, which then execute the steps defined in your workflow.

### Types of Runners

1. **GitHub-Hosted Runners**: Managed by GitHub, automatically provisioned
2. **Self-Hosted Runners**: Managed by you, running on your own infrastructure

---

## GitHub-Hosted vs Self-Hosted Runners

### GitHub-Hosted Runners

**What are they?**
Virtual machines provided and managed by GitHub that run your workflows.

#### Available Environments

| Runner Image | Operating System | Specifications |
|--------------|------------------|----------------|
| `ubuntu-latest` | Ubuntu 22.04 | 2-core CPU, 7 GB RAM, 14 GB SSD |
| `ubuntu-22.04` | Ubuntu 22.04 | 2-core CPU, 7 GB RAM, 14 GB SSD |
| `ubuntu-20.04` | Ubuntu 20.04 | 2-core CPU, 7 GB RAM, 14 GB SSD |
| `windows-latest` | Windows Server 2022 | 2-core CPU, 7 GB RAM, 14 GB SSD |
| `windows-2022` | Windows Server 2022 | 2-core CPU, 7 GB RAM, 14 GB SSD |
| `windows-2019` | Windows Server 2019 | 2-core CPU, 7 GB RAM, 14 GB SSD |
| `macos-latest` | macOS 14 (Sonoma) | 3-core CPU, 14 GB RAM, 14 GB SSD |
| `macos-14` | macOS 14 (Sonoma) | 3-core CPU, 14 GB RAM, 14 GB SSD |
| `macos-13` | macOS 13 (Ventura) | 3-core CPU, 14 GB RAM, 14 GB SSD |
| `macos-12` | macOS 12 (Monterey) | 3-core CPU, 14 GB RAM, 14 GB SSD |

#### Benefits of GitHub-Hosted Runners

✅ **Zero Maintenance**
- No setup required
- No updates or patches needed
- No infrastructure management
- GitHub handles everything

✅ **Clean Environment**
- Fresh virtual machine for every job
- No state carried between runs
- Consistent, predictable environment
- No cleanup needed

✅ **Pre-installed Software**
- Common programming languages (Node.js, Python, Ruby, Java, Go, PHP, etc.)
- Package managers (npm, pip, Maven, Gradle, etc.)
- Build tools (Docker, CMake, GCC, etc.)
- Databases (PostgreSQL, MySQL, MongoDB, etc.)
- Cloud CLIs (AWS, Azure, GCP)

✅ **Scalability**
- Automatic scaling based on demand
- Run multiple jobs in parallel
- No capacity planning needed
- Global availability

✅ **Cost for Public Repositories**
- **Completely free** for public repositories
- Unlimited minutes and concurrent jobs

✅ **Quick Start**
- Start using immediately
- No configuration required
- No infrastructure setup

#### Limitations of GitHub-Hosted Runners

❌ **Limited Resources**
- Fixed CPU: 2-core (3-core for macOS)
- Fixed RAM: 7 GB (14 GB for macOS)
- Fixed storage: 14 GB SSD
- Cannot customize hardware

❌ **No Persistent Storage**
- Fresh environment every run
- Cannot cache between jobs
- Must download dependencies each time

❌ **Limited Customization**
- Cannot install custom software permanently
- Cannot modify system configuration
- Limited control over environment

❌ **Network Restrictions**
- Cannot access private networks
- Cannot connect to on-premises resources
- IP addresses change frequently
- Cannot whitelist IP addresses easily

❌ **Cost for Private Repositories**
- Free tier: 2,000 minutes/month (Pro plan)
- Additional minutes cost money
- macOS runners cost 10x Linux runners
- Windows runners cost 2x Linux runners

❌ **Job Time Limits**
- Maximum job execution time: 6 hours
- Maximum workflow run time: 72 hours
- Can be problematic for long-running tasks

❌ **No GPU Support**
- No GPU acceleration available
- Not suitable for ML/AI workloads

#### Pricing for GitHub-Hosted Runners (Private Repos)

| Plan | Free Minutes/Month | Cost After Free Tier |
|------|-------------------|---------------------|
| Free | 0 | N/A |
| Pro | 3,000 | $0.008/minute (Linux) |
| Team | 3,000 | $0.008/minute (Linux) |
| Enterprise | 50,000 | $0.008/minute (Linux) |

**Multipliers:**
- Linux: 1x ($0.008/min)
- Windows: 2x ($0.016/min)
- macOS: 10x ($0.080/min)

---

### Self-Hosted Runners

**What are they?**
Servers that you own and manage, configured to execute GitHub Actions workflows.

#### Benefits of Self-Hosted Runners

✅ **Custom Hardware**
- Choose CPU, RAM, storage specifications
- Add GPU for ML/AI workloads
- High-performance computing
- Optimize for your workload

✅ **Persistent Environment**
- Keep software installed between runs
- Cache dependencies locally
- Faster subsequent builds
- Pre-warm environments

✅ **Network Access**
- Access private networks and VPCs
- Connect to on-premises resources
- Access internal databases
- Whitelist static IP addresses

✅ **Cost Control**
- Pay for infrastructure only
- No per-minute charges for Actions
- Potentially cheaper for heavy usage
- Use existing infrastructure

✅ **Custom Software**
- Install any software or tools
- Use specific versions permanently
- Custom security tools
- Proprietary software

✅ **No Time Limits**
- Run jobs as long as needed
- No 6-hour job limit
- Long-running tasks supported

✅ **Compliance**
- Meet data residency requirements
- Control data location
- Custom security policies
- Audit and logging control

✅ **Dedicated Resources**
- No resource contention
- Guaranteed performance
- Predictable execution times

#### Limitations of Self-Hosted Runners

❌ **Maintenance Overhead**
- Must setup and configure
- Need to update and patch
- Monitor health and performance
- Manage infrastructure

❌ **Security Responsibility**
- You secure the runner
- Risk of running untrusted code
- Need to harden environment
- Manage access controls

❌ **No Automatic Scaling**
- Must provision capacity
- Handle load manually
- Cannot auto-scale easily
- Need capacity planning

❌ **Infrastructure Cost**
- Pay for servers even when idle
- Cloud hosting costs
- Network costs
- Storage costs

❌ **Complexity**
- Requires DevOps knowledge
- Need monitoring and alerting
- Troubleshooting required
- Backup and disaster recovery

❌ **Manual Cleanup**
- Environment not reset automatically
- Must clean up after jobs
- Potential state conflicts
- Disk space management

❌ **No Built-in HA**
- No automatic failover
- Need to implement redundancy
- Single point of failure
- Manual recovery

---

### Comparison Summary

| Feature | GitHub-Hosted | Self-Hosted |
|---------|---------------|-------------|
| **Setup** | None required | Manual setup needed |
| **Maintenance** | GitHub manages | You manage |
| **Cost (Public)** | Free | Infrastructure cost |
| **Cost (Private, Light)** | Free tier, then pay | Infrastructure cost |
| **Cost (Private, Heavy)** | Expensive | Potentially cheaper |
| **Hardware** | Fixed (2-core, 7GB) | Customizable |
| **GPU Support** | No | Yes |
| **Persistent Storage** | No | Yes |
| **Caching** | Limited | Full control |
| **Network Access** | Public only | Private networks |
| **Custom Software** | Temporary only | Permanent |
| **Job Time Limit** | 6 hours | Unlimited |
| **Environment** | Clean every run | Persistent |
| **Scaling** | Automatic | Manual |
| **Security** | GitHub handles | Your responsibility |
| **IP Addresses** | Dynamic | Static possible |
| **Updates** | Automatic | Manual |
| **Compliance** | Limited control | Full control |

---

### When to Use GitHub-Hosted Runners

Use GitHub-hosted runners when:
- ✅ Building open source projects (free forever)
- ✅ Standard CI/CD workflows (build, test, deploy)
- ✅ Don't need special hardware or software
- ✅ Want zero maintenance
- ✅ Don't need private network access
- ✅ Jobs complete within 6 hours
- ✅ Limited Actions usage (within free tier)
- ✅ Need clean environment every run

---

### When to Use Self-Hosted Runners

Use self-hosted runners when:
- ✅ Need custom hardware (GPUs, high CPU/RAM)
- ✅ Heavy usage (cheaper than GitHub minutes)
- ✅ Need private network/VPC access
- ✅ Need persistent environment/caching
- ✅ Jobs take longer than 6 hours
- ✅ Need specific software/tools installed
- ✅ Compliance or data residency requirements
- ✅ Need static IP addresses
- ✅ Have existing infrastructure to leverage
- ✅ Want full control over environment

---

## Setting Up Self-Hosted Runner on AWS EC2

Let's set up a self-hosted runner on an AWS Ubuntu EC2 instance step by step.

### Prerequisites

- AWS account with EC2 access
- GitHub repository with admin permissions
- Basic knowledge of Linux commands
- SSH client installed

---

### Step 1: Launch EC2 Instance

#### 1.1 Choose Instance Configuration

**Recommended Specifications:**

For light workloads:
- **Instance Type**: t3.medium (2 vCPU, 4 GB RAM)
- **Storage**: 30 GB gp3

For moderate workloads:
- **Instance Type**: t3.large (2 vCPU, 8 GB RAM)
- **Storage**: 50-100 GB gp3

For heavy workloads:
- **Instance Type**: t3.xlarge (4 vCPU, 16 GB RAM) or larger
- **Storage**: 100-200 GB gp3

#### 1.2 Launch Instance via AWS Console

1. Go to **EC2 Dashboard** → **Launch Instance**

2. **Configure Instance:**
   ```
   Name: github-actions-runner
   AMI: Ubuntu Server 22.04 LTS (64-bit x86)
   Instance Type: t3.medium (or your choice)
   Key Pair: Create or select existing
   ```

3. **Network Settings:**
   ```
   VPC: Default or your VPC
   Subnet: Public subnet (if needs internet)
   Auto-assign Public IP: Enable
   ```

4. **Security Group:**
   ```
   Allow SSH (port 22) from your IP
   Allow outbound HTTPS (port 443) - for GitHub
   ```

5. **Storage:**
   ```
   Root Volume: 30-50 GB gp3
   ```

6. **Launch Instance**

#### 1.3 Connect to EC2 Instance

```bash
# SSH into your instance
ssh -i "your-key.pem" ubuntu@your-ec2-public-ip
```

---

### Step 2: Prepare EC2 Instance

#### 2.1 Update System

```bash
# Update package lists
sudo apt update

# Upgrade installed packages
sudo apt upgrade -y

# Install essential packages
sudo apt install -y curl wget git jq build-essential
```

#### 2.2 Install Docker (Optional but Recommended)

Many workflows use Docker, so it's good to have it installed.

```bash
# Install Docker
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# Add ubuntu user to docker group
sudo usermod -aG docker ubuntu

# Start Docker service
sudo systemctl enable docker
sudo systemctl start docker

# Verify installation
docker --version
```

**Log out and back in** for docker group changes to take effect:
```bash
exit
# Then SSH back in
ssh -i "your-key.pem" ubuntu@your-ec2-public-ip
```

#### 2.3 Install Additional Tools (Optional)

Based on your workflow needs:

```bash
# Node.js and npm
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs

# Python and pip
sudo apt install -y python3 python3-pip

# AWS CLI
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install

# Verify installations
node --version
python3 --version
aws --version
```

---

### Step 3: Add Runner to GitHub Repository

#### 3.1 Get Runner Token from GitHub

1. Go to your GitHub repository
2. Click **Settings** (repository settings)
3. In left sidebar, click **Actions** → **Runners**
4. Click **New self-hosted runner** button
5. Select **Linux** as the operating system
6. Copy the commands shown (we'll use these next)

#### 3.2 Download and Configure Runner

On your EC2 instance, run these commands (adjust with your specific values from GitHub):

```bash
# Create a directory for the runner
mkdir actions-runner && cd actions-runner

# Download the runner package (check GitHub for latest version)
curl -o actions-runner-linux-x64-2.311.0.tar.gz -L https://github.com/actions/runner/releases/download/v2.311.0/actions-runner-linux-x64-2.311.0.tar.gz

# Extract the installer
tar xzf ./actions-runner-linux-x64-2.311.0.tar.gz
```

#### 3.3 Configure the Runner

```bash
# Configure the runner
# Replace with your actual repository URL and token from GitHub
./config.sh --url https://github.com/YOUR_USERNAME/YOUR_REPO --token YOUR_TOKEN

# You'll be prompted with questions:
# - Enter the name of runner group: [Press Enter for default]
# - Enter the name of runner: [Give it a meaningful name, e.g., "aws-ec2-runner"]
# - Enter any additional labels: [e.g., "aws,ubuntu,self-hosted"]
# - Enter name of work folder: [Press Enter for default: _work]
```

#### 3.4 Test the Runner

```bash
# Run the runner interactively to test
./run.sh
```

You should see:
```
√ Connected to GitHub

Current runner version: '2.311.0'
2024-12-23 10:00:00Z: Listening for Jobs
```

**Test it**: Go to GitHub Actions and trigger a workflow. You should see it run on your self-hosted runner.

Press `Ctrl+C` to stop the interactive runner.

---

### Step 4: Install Runner as a Service

To run the runner automatically and persist across reboots:

```bash
# Install the runner as a service
sudo ./svc.sh install

# Start the service
sudo ./svc.sh start

# Check status
sudo ./svc.sh status

# Service will now start automatically on boot
```

#### Service Management Commands

```bash
# Check status
sudo ./svc.sh status

# Stop the service
sudo ./svc.sh stop

# Start the service
sudo ./svc.sh start

# Restart the service
sudo ./svc.sh restart

# Uninstall the service
sudo ./svc.sh uninstall
```

---

### Step 5: Use Self-Hosted Runner in Workflows

Update your workflow to use the self-hosted runner:

```yaml
name: CI with Self-Hosted Runner

on:
  push:
    branches: [main]

jobs:
  build:
    # Use self-hosted runner
    runs-on: self-hosted
    
    # Or use specific labels
    # runs-on: [self-hosted, linux, aws]
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      
      - name: Show runner info
        run: |
          echo "Running on: $(hostname)"
          echo "IP Address: $(hostname -I)"
          echo "CPU: $(nproc) cores"
          echo "Memory: $(free -h | awk '/^Mem:/ {print $2}')"
          echo "Disk: $(df -h / | awk 'NR==2 {print $4}' )"
      
      - name: Build application
        run: |
          echo "Building with custom runner..."
          # Your build commands here
```

#### Using Labels

You can target specific runners using labels:

```yaml
jobs:
  build-gpu:
    runs-on: [self-hosted, linux, gpu]
    steps:
      - run: nvidia-smi  # GPU info
  
  build-aws:
    runs-on: [self-hosted, aws, ubuntu]
    steps:
      - run: aws --version
  
  build-high-memory:
    runs-on: [self-hosted, high-memory]
    steps:
      - run: free -h
```

---

### Step 6: Configure Runner Groups (Organization/Enterprise)

For organizations, you can create runner groups to control access:

1. Go to **Organization Settings** → **Actions** → **Runner groups**
2. Click **New runner group**
3. Configure:
   - Name: e.g., "Production Runners"
   - Access: Select which repositories can use
   - Runners: Assign runners to this group

Usage in workflow:
```yaml
jobs:
  deploy:
    runs-on: 
      group: production-runners
      labels: [self-hosted, linux]
```

---

## Managing Self-Hosted Runners

### Monitoring Runner Health

#### Check Runner Status via GitHub

1. Repository → **Settings** → **Actions** → **Runners**
2. You'll see:
   - Runner status (Online/Offline)
   - Current job (if running)
   - Last seen timestamp

#### Monitor on EC2 Instance

```bash
# Check runner service status
sudo systemctl status actions.runner.*

# View runner logs
sudo journalctl -u actions.runner.* -f

# Check disk space
df -h

# Check memory usage
free -h

# Check CPU usage
top
```

### Updating Self-Hosted Runners

GitHub will notify you when updates are available.

```bash
# Stop the runner service
sudo ./svc.sh stop

# Download latest version
cd ~/actions-runner
curl -o actions-runner-linux-x64-2.XXX.X.tar.gz -L https://github.com/actions/runner/releases/download/vX.XXX.X/actions-runner-linux-x64-2.XXX.X.tar.gz

# Extract new version
tar xzf ./actions-runner-linux-x64-2.XXX.X.tar.gz

# Start the service
sudo ./svc.sh start
```

### Scaling Multiple Runners

#### Option 1: Multiple Runners on Same Instance

```bash
# Create multiple runner directories
mkdir ~/actions-runner-1 ~/actions-runner-2

# Configure each separately
cd ~/actions-runner-1
./config.sh --url YOUR_REPO --token TOKEN_1 --name runner-1

cd ~/actions-runner-2
./config.sh --url YOUR_REPO --token TOKEN_2 --name runner-2

# Install both as services
cd ~/actions-runner-1 && sudo ./svc.sh install
cd ~/actions-runner-2 && sudo ./svc.sh install

# Start both
sudo systemctl start actions.runner.*
```

#### Option 2: Multiple EC2 Instances

Launch additional EC2 instances and configure each as a runner. Use Auto Scaling Groups for dynamic scaling.

### Cleaning Up Runner Environment

Self-hosted runners don't clean automatically, so implement cleanup:

```bash
# Create cleanup script
cat > ~/cleanup-runner.sh << 'EOF'
#!/bin/bash

# Remove old Docker containers
docker container prune -f

# Remove old Docker images
docker image prune -af

# Clean up work directory
cd ~/actions-runner/_work
find . -mindepth 1 -maxdepth 1 -mtime +7 -exec rm -rf {} \;

# Clean package manager caches
sudo apt clean
sudo apt autoclean

echo "Cleanup completed at $(date)"
EOF

chmod +x ~/cleanup-runner.sh

# Schedule with cron (daily at 2 AM)
(crontab -l 2>/dev/null; echo "0 2 * * * /home/ubuntu/cleanup-runner.sh >> /var/log/runner-cleanup.log 2>&1") | crontab -
```

---

## Best Practices and Security

### Security Best Practices

#### 1. Use Separate Runners for Different Trust Levels

```
Production Runners → Trusted code only
Staging Runners → Internal PRs
Development Runners → All PRs including forks
```

#### 2. Never Use Self-Hosted Runners for Public Repos

⚠️ **Critical Warning**: Do NOT use self-hosted runners with public repositories that accept pull requests from forks. Malicious actors can submit PRs with code that:
- Steals secrets
- Mines cryptocurrency
- Attacks your infrastructure
- Exfiltrates data

**Only use self-hosted runners for:**
- Private repositories
- Public repositories that don't accept PRs
- Public repositories with strict PR approval process

#### 3. Isolate Runners

```bash
# Use separate IAM roles for EC2 instances
# Limit network access with Security Groups
# Use separate VPCs for production runners
```

#### 4. Implement Network Security

**Security Group Configuration:**
```
Inbound:
- SSH (22) from your IP only
- No other inbound rules needed

Outbound:
- HTTPS (443) to github.com
- Specific ports for your use case
- Deny all other outbound by default
```

#### 5. Regular Updates and Patching

```bash
# Enable automatic security updates
sudo apt install unattended-upgrades
sudo dpkg-reconfigure --priority=low unattended-upgrades

# Regular manual updates
sudo apt update && sudo apt upgrade -y
```

#### 6. Use Secrets Safely

```yaml
jobs:
  deploy:
    runs-on: self-hosted
    steps:
      - name: Use secrets
        run: |
          # Secrets are automatically masked in logs
          echo "Token: ${{ secrets.API_TOKEN }}"  # Shows ****
        env:
          API_TOKEN: ${{ secrets.API_TOKEN }}
```

#### 7. Implement Least Privilege

```bash
# Don't run runner as root
# Use dedicated user with minimal permissions
# Use IAM roles instead of access keys
```

#### 8. Monitor and Audit

```bash
# Enable CloudWatch for EC2 monitoring
# Set up alerts for unusual activity
# Review workflow logs regularly
# Monitor resource usage
```

---

### Cost Optimization

#### 1. Use Spot Instances (For Non-Critical Workloads)

```bash
# Launch EC2 Spot Instance (60-90% cheaper)
# Configure runner to handle interruptions gracefully
```

#### 2. Auto-Scaling Based on Queue

Use AWS Lambda or scripts to:
- Start runners when jobs are queued
- Stop runners when idle
- Use EC2 Auto Scaling Groups

#### 3. Right-Size Instances

Monitor usage and adjust:
```bash
# Check CPU usage over time
aws cloudwatch get-metric-statistics \
  --namespace AWS/EC2 \
  --metric-name CPUUtilization \
  --dimensions Name=InstanceId,Value=i-1234567890abcdef0 \
  --start-time 2024-12-01T00:00:00Z \
  --end-time 2024-12-23T23:59:59Z \
  --period 3600 \
  --statistics Average
```

#### 4. Use Reserved Instances

For always-on runners, use Reserved Instances for up to 72% savings.

---

## Troubleshooting

### Common Issues

#### 1. Runner Shows Offline

**Symptoms:**
- Runner shows offline in GitHub
- No jobs are picked up

**Solutions:**
```bash
# Check service status
sudo systemctl status actions.runner.*

# Check logs
sudo journalctl -u actions.runner.* -n 100 --no-pager

# Restart service
sudo ./svc.sh restart

# Check network connectivity
curl -I https://github.com
```

#### 2. Jobs Not Starting

**Symptoms:**
- Jobs stay queued
- Not assigned to runner

**Solutions:**
```yaml
# Check workflow uses correct labels
runs-on: self-hosted  # Not 'ubuntu-latest'

# Check runner labels match
runs-on: [self-hosted, linux, aws]  # Must match configured labels
```

#### 3. Disk Space Full

**Symptoms:**
```
Error: No space left on device
```

**Solutions:**
```bash
# Check disk space
df -h

# Clean Docker
docker system prune -af --volumes

# Clean runner work directory
cd ~/actions-runner/_work
rm -rf *

# Increase EBS volume size
# AWS Console → EC2 → Volumes → Modify Volume
```

#### 4. Permission Errors

**Symptoms:**
```
Permission denied
```

**Solutions:**
```bash
# Ensure ubuntu user owns runner directory
sudo chown -R ubuntu:ubuntu ~/actions-runner

# Check Docker group membership
groups ubuntu
# Should include 'docker'

# Re-add to docker group if needed
sudo usermod -aG docker ubuntu
# Then logout and login
```

#### 5. Runner Crashes or Hangs

**Symptoms:**
- Runner stops responding
- Service crashes repeatedly

**Solutions:**
```bash
# Check system resources
free -h      # Memory
df -h        # Disk
top          # CPU

# Check for OOM (Out of Memory) kills
dmesg | grep -i "out of memory"
sudo grep -i "out of memory" /var/log/syslog

# Increase instance size if needed
# Or limit concurrent jobs
```

#### 6. Network Connectivity Issues

**Symptoms:**
```
Failed to connect to github.com
```

**Solutions:**
```bash
# Check internet connectivity
ping -c 4 8.8.8.8

# Check DNS resolution
nslookup github.com

# Check HTTPS connectivity
curl -v https://api.github.com

# Check Security Group allows outbound HTTPS
# AWS Console → EC2 → Security Groups
```

#### 7. Runner Token Expired

**Symptoms:**
```
Error: The runner registration token has expired
```

**Solutions:**
1. Go to GitHub → Repository → Settings → Actions → Runners
2. Click **New self-hosted runner**
3. Copy the new token
4. Reconfigure runner:
```bash
cd ~/actions-runner
./config.sh remove --token OLD_TOKEN
./config.sh --url YOUR_REPO_URL --token NEW_TOKEN
sudo ./svc.sh install
sudo ./svc.sh start
```

---

### Debugging Commands

```bash
# View runner configuration
cat ~/actions-runner/.runner

# View runner credentials (sensitive!)
cat ~/actions-runner/.credentials

# Check runner process
ps aux | grep Runner.Listener

# Check all runner services
systemctl list-units | grep actions.runner

# View all logs
sudo journalctl -u actions.runner.* --since "1 hour ago"

# Monitor real-time logs
sudo journalctl -u actions.runner.* -f

# Check network statistics
ss -tuln | grep LISTEN
netstat -an | grep ESTABLISHED
```

---

## Advanced Configuration

### Using Runner in Docker Container

```dockerfile
FROM ubuntu:22.04

RUN apt-get update && apt-get install -y \
    curl \
    git \
    jq \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /actions-runner

RUN curl -o actions-runner-linux-x64-2.311.0.tar.gz -L \
    https://github.com/actions/runner/releases/download/v2.311.0/actions-runner-linux-x64-2.311.0.tar.gz \
    && tar xzf ./actions-runner-linux-x64-2.311.0.tar.gz \
    && rm actions-runner-linux-x64-2.311.0.tar.gz

COPY start.sh /start.sh
RUN chmod +x /start.sh

CMD ["/start.sh"]
```

### Auto-Scaling with Terraform

```hcl
# Example Terraform configuration for auto-scaling runners
resource "aws_autoscaling_group" "github_runners" {
  name                = "github-actions-runners"
  min_size            = 1
  max_size            = 10
  desired_capacity    = 2
  vpc_zone_identifier = [aws_subnet.private.id]

  launch_template {
    id      = aws_launch_template.runner.id
    version = "$Latest"
  }

  tag {
    key                 = "Name"
    value               = "github-actions-runner"
    propagate_at_launch = true
  }
}
```

---

## Summary

### Quick Decision Guide

**Choose GitHub-Hosted Runners if:**
- Building public/open source projects
- Standard workflows with common languages
- Want zero maintenance
- Low to moderate Actions usage
- Don't need special hardware

**Choose Self-Hosted Runners if:**
- Heavy Actions usage (cost savings)
- Need GPUs or specific hardware
- Need private network access
- Need persistent caching
- Jobs exceed 6 hours
- Need compliance/control

### Key Takeaways

1. **GitHub-hosted runners** are great for most use cases - zero setup, automatic maintenance, and free for public repos
2. **Self-hosted runners** offer more control, can be cost-effective at scale, but require management
3. **Never use self-hosted runners** with public repositories that accept PRs from forks
4. **Security is your responsibility** with self-hosted runners
5. **Monitor and maintain** self-hosted runners regularly
6. **Start with GitHub-hosted**, move to self-hosted only when needed

---

## 🧑‍💻 Author
**Md. Sarowar Alam**  
Lead DevOps Engineer, Hogarth Worldwide  
📧 Email: sarowar@hotmail.com  
🔗 LinkedIn: [linkedin.com/in/sarowar](https://www.linkedin.com/in/sarowar/)
