# AWS Flask & Express CI/CD Deployment

## 📋 Project Overview

This project demonstrates a fully functional CI/CD pipeline for deploying a Flask backend and an Express frontend on a single EC2 instance using Jenkins.

Both applications are managed with PM2 to ensure they remain active and restart on failure.

---

## 📁 Repository Structure

```
jenkins/
├── backend/
│   ├── app.py
│   ├── business.py
│   ├── templates/index.html
│   └── Jenkinsfile-flask      ← Add this file
├── frontend/
│   ├── app.js
│   ├── views/index.ejs
│   └── Jenkinsfile-express    ← Add this file
```

---

## 🏗️ Architecture Diagram

```
             ┌───────────────┐
             │ GitHub Repo   │
             │ aws/          │
             └──────┬────────┘
                    │ Push Code
                    ▼
             ┌───────────────┐
             │ Jenkins CI/CD │
             │ Pipelines:    │
             │ - Flask       │
             │ - Express     │
             └──────┬────────┘
                    │ Deploy
                    ▼
             ┌───────────────┐
             │ AWS EC2       │
             │ Ubuntu        │
             │ PM2 running   │
             │ Flask + Node  │
             └──────┬────────┘
                    │ Access via Public IP
          ┌─────────┴───────────┐
          │                     │
   http://EC2_IP:8000       http://EC2_IP:3000
   Flask Backend             Express Frontend
```

---

## ✅ Prerequisites

- AWS EC2 instance (Ubuntu)
- Security Group configured to allow:
  - Port 22 (SSH)
  - Port 8080 (Jenkins)
  - Port 8000 (Flask)
  - Port 3000 (Express)
- Python 3.x installed
- Node.js and npm installed

---

## 🚀 Installation Steps

### 1️⃣ Install PM2

```bash
sudo npm install -g pm2
```

### 2️⃣ Install Jenkins

```bash
wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
sudo sh -c 'echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt install -y jenkins
sudo systemctl start jenkins
sudo systemctl enable jenkins
```

### 3️⃣ Access Jenkins

Navigate to:
```
http://<EC2_PUBLIC_IP>:8080
```

Retrieve initial admin password:
```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

---

## 🔧 Application Deployment with PM2

### Backend (Flask)

```bash
cd ~/aws/backend
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
pm2 start "venv/bin/python app.py" --name flask-app
pm2 save
pm2 startup
```

### Frontend (Express)

```bash
cd ~/aws/frontend
npm install
pm2 start "node app.js" --name express-app
pm2 save
pm2 startup
```

### Check PM2 Status

```bash
pm2 status
```

---

## ⚙️ Jenkins CI/CD Pipelines

### 1️⃣ Jenkinsfile for Flask (backend)

Create `backend/Jenkinsfile-flask`



### 2️⃣ Jenkinsfile for Express (frontend)

Create `frontend/Jenkinsfile-express`:



## 🔗 GitHub Webhook Setup

1. Go to your GitHub repository settings
2. Navigate to **Webhooks** → **Add webhook**
3. Configure:
   - **Payload URL**: `http://<EC2_PUBLIC_IP>:8080/github-webhook/`
   - **Content type**: `application/json`
   - **Events**: Select "Just the push event"
4. Click **Add webhook**

**Note**: Ensure your EC2 Security Group allows inbound traffic on port 8080 for Jenkins.

---

## ✅ Verify Applications

Open in your browser:

- **Flask Backend**: `http://<EC2_PUBLIC_IP>:8000`
- **Express Frontend**: `http://<EC2_PUBLIC_IP>:3000`

---
