# GitHub Actions - EC2 Connectivity Check

This repository demonstrates how to use GitHub Actions to automatically check connectivity to an AWS EC2 instance and extract metadata information on every push to the main branch.

## 🎯 What This Does

The workflow automatically:
- ✅ Tests SSH connectivity to your EC2 instance
- 🔐 Securely connects using SSH key authentication
- 📊 Extracts and displays EC2 metadata including:
  - Public IP address
  - Private IP address
  - Hostname
  - Instance ID
  - Availability Zone
  - Instance Type
  - System uptime

## 🚀 Quick Start Guide

### Prerequisites

1. An AWS EC2 instance with SSH access enabled
2. A GitHub account
3. SSH key pair for EC2 access

### Step 1: Initial Setup on Your Local Machine

Navigate to your project directory:
```bash
cd "c:\CLOUD\OneDrive - Hogarth Worldwide\Documents\Ostad\Batch-08\GiT\github-actions-hello-world"
```

### Step 2: Initialize Git Repository

```bash
# Initialize git repository
git init

# Add all files
git add .

# Create initial commit
git commit -m "Initial commit: EC2 connectivity check workflow"
```

### Step 3: Create GitHub Repository

1. Go to [GitHub](https://github.com) and log in
2. Click the **"+"** icon in the top-right corner
3. Select **"New repository"**
4. Fill in the details:
   - **Repository name**: `github-actions-hello-world` (or your preferred name)
   - **Description**: "EC2 connectivity check using GitHub Actions"
   - **Visibility**: Choose Public or Private
   - **DO NOT** initialize with README, .gitignore, or license (we already have files)
5. Click **"Create repository"**

### Step 4: Link and Push to GitHub

GitHub will show you commands to push an existing repository. Use these:

```bash
# Add GitHub remote (replace YOUR_USERNAME with your GitHub username)
git remote add origin https://github.com/YOUR_USERNAME/github-actions-hello-world.git

# Rename branch to main (if needed)
git branch -M main

# Push to GitHub
git push -u origin main
```

### Step 5: Configure GitHub Secrets

This is the most important step! Your workflow needs three secrets to connect to your EC2 instance.

1. Go to your repository on GitHub
2. Click **Settings** (top navigation bar)
3. In the left sidebar, click **Secrets and variables** → **Actions**
4. Click **"New repository secret"** button
5. Add the following three secrets:

#### Secret 1: EC2_HOST
- **Name**: `EC2_HOST`
- **Value**: Your EC2 instance's public IP address (e.g., `54.123.45.67`)
- Click **"Add secret"**

#### Secret 2: EC2_USER
- **Name**: `EC2_USER`
- **Value**: Your SSH username (e.g., `ubuntu`, `ec2-user`, `admin`)
- Click **"Add secret"**

#### Secret 3: EC2_SSH_KEY
- **Name**: `EC2_SSH_KEY`
- **Value**: Your complete private SSH key
- To get your private key content:
  ```bash
  # On Windows (PowerShell)
  Get-Content "path\to\your\key.pem"
  
  # Copy the entire output including:
  # -----BEGIN RSA PRIVATE KEY-----
  # (all the key content)
  # -----END RSA PRIVATE KEY-----
  ```
- Paste the entire key content (including BEGIN and END lines)
- Click **"Add secret"**

## 🔄 How It Works

### Workflow Trigger
The workflow is triggered automatically on every push to the `main` branch:
```yaml
on:
  push:
    branches:
      - main
```

### Workflow Steps

1. **Checkout Repository**: Gets the latest code from your repo
2. **Setup SSH Key**: 
   - Creates SSH directory structure
   - Securely writes the SSH key from secrets
   - Sets proper permissions (600)
   - Adds EC2 host to known_hosts
3. **Test EC2 Connectivity**: Performs a simple SSH connection test
4. **Extract EC2 Metadata**: 
   - Connects to EC2 instance
   - Uses AWS metadata service (169.254.169.254) to fetch instance details
   - Displays comprehensive system information
5. **Cleanup SSH Key**: Removes the SSH key file for security (always runs)

## 📋 Viewing Workflow Results

After pushing to GitHub:

1. Go to your repository on GitHub
2. Click the **Actions** tab (top navigation)
3. You'll see your workflow runs listed
4. Click on any run to see detailed logs
5. Expand the "Extract EC2 Metadata" step to view all the information

## 🔒 Security Best Practices

✅ **DO:**
- Store sensitive information (SSH keys, IPs, usernames) in GitHub Secrets
- Use SSH key authentication instead of passwords
- Set proper file permissions for SSH keys (600)
- Clean up SSH keys after use

❌ **DON'T:**
- Commit SSH keys or credentials to the repository
- Expose IP addresses or sensitive data in code
- Use weak authentication methods

## 🛠️ Customization

### Change Trigger to Other Branches
Edit [.github/workflows/ec2-connectivity-check.yml](.github/workflows/ec2-connectivity-check.yml):
```yaml
on:
  push:
    branches:
      - main
      - develop  # Add more branches
```

### Add Schedule (Run at Specific Times)
```yaml
on:
  push:
    branches:
      - main
  schedule:
    - cron: '0 0 * * *'  # Runs daily at midnight UTC
```

### Add Manual Trigger
```yaml
on:
  push:
    branches:
      - main
  workflow_dispatch:  # Allows manual trigger from GitHub UI
```

## 📁 Repository Structure

```
.
├── .github/
│   └── workflows/
│       └── ec2-connectivity-check.yml    # Main workflow file
├── .gitignore                            # Files to ignore in git
└── README.md                             # This file
```

## 🐛 Troubleshooting

### Workflow Fails with "Permission denied (publickey)"
- Verify that `EC2_SSH_KEY` contains the complete private key
- Check that `EC2_USER` matches your EC2 instance's SSH user
- Ensure the corresponding public key is in `~/.ssh/authorized_keys` on your EC2

### Workflow Fails with "Connection timed out"
- Verify `EC2_HOST` has the correct IP address
- Check EC2 security group allows SSH (port 22) from GitHub Actions IPs
- Ensure your EC2 instance is running

### Metadata Service Returns Errors
- Ensure your EC2 instance has the metadata service enabled
- Some instance types might have IMDSv2 requirements (this workflow uses IMDSv1)

## 📚 Learn More

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [AWS EC2 Instance Metadata Service](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-metadata.html)
- [GitHub Encrypted Secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets)

## 📝 License

This project is open source and available under the [MIT License](LICENSE).

## 🤝 Contributing

Feel free to fork this repository and submit pull requests for improvements!

---

**Made with ❤️ for learning GitHub Actions and AWS EC2 automation**
