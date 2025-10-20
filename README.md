# 🧩 Install New Relic Monitoring on Existing Server

This repository automates the installation and configuration of **New Relic Infrastructure and Java agents** on an existing server using **GitHub Actions** and **Ansible**.
It enables monitoring of system metrics, application performance, and logs for your Spring Boot application (like *Spring PetClinic*).

---

## 🚀 Features

* Installs **New Relic Infrastructure Agent**
* Configures **log forwarding** (system + app logs)
* Installs and configures **New Relic Java Agent**
* Deploys and manages the **Spring Boot application** via systemd
* Fully automated via **GitHub Actions + Ansible**
* Can also be installed **manually** by running the playbook

---

## 🧱 Repository Structure

```
.
├── .github/
│   └── workflows/
│       └── install-newrelic.yml   # GitHub Actions workflow
├── ansible/
│   └── install-newrelic.yml       # Ansible Playbook
├── .env                           # Environment variables (server details)
└── README.md
```

---

## ⚙️ Prerequisites

Before running the workflow or manual setup, ensure you have:

* A **running Linux server** (Ubuntu/Debian recommended)
* SSH access to that server
* **Root or sudo privileges**
* Your **New Relic License Key**
* Your Spring Boot app deployed on the server (optional if only monitoring system metrics)

---

## 🔐 GitHub Secrets Required (for automated workflow)

Set these secrets in your GitHub repository under:
`Settings → Secrets and variables → Actions → New repository secret`

| Secret Name             | Description                                     |
| ----------------------- | ----------------------------------------------- |
| `SSH_KEY`               | Private key for SSH access to the target server |
| `NEW_RELIC_LICENSE_KEY` | Your New Relic account license key              |
| `NEW_RELIC_REGION`      | (Optional) Region - either `US` or `EU`         |

---

## 🧾 Environment Variables (.env)

Create a `.env` file in the root directory with your server connection details:

```bash
SERVER_IP=<YOUR_SERVER_PUBLIC_IP>
SERVER_USER=ubuntu
```

> ⚠️ Do not commit your `.env` file to GitHub. It contains sensitive data.

---

## ⚡ Automated Installation via GitHub Actions

This workflow:

1. **Checks out** the repository
2. **Installs Ansible** and dependencies
3. **Runs the Ansible playbook** on your target server via SSH

### Manual Trigger

The workflow uses a manual trigger (`workflow_dispatch`), so you can start it from the GitHub Actions tab.

**Steps:**

1. Push this code to GitHub.
2. Go to **Actions tab → Install New Relic Monitoring on Existing Server**.
3. Click **“Run workflow”** → confirm to execute.
4. GitHub Actions will connect to your server, install New Relic agents, and start monitoring automatically.

---

## 🧩 Ansible Playbook Overview (`ansible/install-newrelic.yml`)

This playbook performs the following steps on your target server:

1. Installs prerequisites (`apt-transport-https`, `unzip`, etc.)
2. Adds New Relic’s apt repository and GPG key
3. Installs and configures the New Relic Infrastructure Agent
4. Sets up system and application log forwarding
5. Downloads and configures the New Relic Java Agent
6. Creates a systemd service for the Spring Boot app
7. Starts and verifies that the application and New Relic agent are running

---

## 🛠 Manual Setup (Running the Playbook)

If you prefer **manual installation** (without GitHub Actions):

### 1️⃣ Clone the repository

```bash
git clone <your-repo-url>
cd <repo-folder>
```

### 2️⃣ Create `.env` file

```bash
SERVER_IP=<YOUR_SERVER_PUBLIC_IP>
SERVER_USER=ubuntu
```

### 3️⃣ Install Ansible on your machine

```bash
sudo apt-get update
sudo apt-get install -y ansible python3-apt sshpass unzip
```

### 4️⃣ Run the Playbook

```bash
export NEW_RELIC_LICENSE_KEY=<YOUR_LICENSE_KEY>
export SERVER_USER=ubuntu
export SERVER_IP=<YOUR_SERVER_IP>

ansible-playbook ansible/install-newrelic.yml \
  -i "${SERVER_IP}," \
  -u "${SERVER_USER}" \
  --private-key <path-to-your-ssh-private-key>
```

✅ This will do **exactly what the GitHub Actions workflow does** but manually.

---

## 🧠 Verification

After successful installation:

* Go to [New Relic Dashboard](https://one.newrelic.com)
* Navigate to **Infrastructure → Hosts** to verify your server is reporting
* Check **APM → Applications** for your Java app (e.g., *SpringPetClinic*)
* Logs should appear under **Logs → Search**

---

## 🧹 Cleanup (Optional)

To remove New Relic agents from the server:

```bash
sudo apt-get remove newrelic-infra -y
sudo rm -rf /opt/newrelic /etc/newrelic-infra*
sudo systemctl disable petclinic
sudo systemctl stop petclinic
```

---
