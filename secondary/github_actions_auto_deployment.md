# Setting up GitHub Actions for Automated Deployment

## 1. Create SSH Keys for GitHub Actions

```bash
# On local workstation: create new SSH key pair for GitHub Actions deployment
ssh-keygen -t ed25519 -C "company-project-environment-github-actions-deploy"

# Add public key to production server's authorized_keys
cat ~/.ssh/id_ed25519.pub | ssh your-user@your-server 'cat >> ~/.ssh/authorized_keys'

# Copy private key content for GitHub Secrets
cat ~/.ssh/id_ed25519
```

## 2. Add Secrets to GitHub Repository

1. Go to your GitHub repository
2. Navigate to Settings → Secrets and variables → Actions
3. Click "New repository secret"
4. Add the following secrets:
   - `HOST`: Your production server IP/domain
   - `USERNAME`: Your production server username
   - `SSH_PRIVATE_KEY`: The private key content you copied earlier

## 3. Create GitHub Actions Workflow

1. In your repository, create `.github/workflows/deploy.yml`
2. Add this content:

```yaml
name: Deploy to Production

on:
  push:
    branches: [main] # or your default branch name

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Deploy to Server
        uses: appleboy/ssh-action@master
        with:
          host: ${{ secrets.HOST }}
          username: ${{ secrets.USERNAME }}
          key: ${{ secrets.SSH_PRIVATE_KEY }}
          script: |
            cd /path/to/your/project
            git pull
            # Add your additional deployment commands here
            # Examples:
            # npm install
            # npm run build
            # pm2 restart your-app
```

## 4. Testing Workflow

1. Make a change to your repository
2. Commit and push to your main branch
3. Check the "Actions" tab in your GitHub repository to monitor the deployment
4. Verify changes on your production server
