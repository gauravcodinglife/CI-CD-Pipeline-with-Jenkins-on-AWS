# 🚀 Production-Grade CI/CD Pipeline with Jenkins on AWS

This repository documents the architecture and implementation of an end-to-end automated CI/CD pipeline using **Jenkins on AWS**. The project implements industry-standard DevOps practices, moving from code commit to production deployment with a focus on security, scalability, and separation of concerns.

## 🏗 Architecture Overview

The system utilizes a **Master-Agent architecture** to ensure stability and performance.

*   **GitHub:** Source Code Management & Webhook triggers.
*   **Jenkins Master (Control Plane):** Orchestrates the pipeline, manages jobs, and distributes tasks.
*   **Jenkins Agent (Execution Node):** Isolated environment for build execution and container hosting.
*   **Docker Runtime:** Handles the containerization and deployment of the application.

## 🔄 Pipeline Workflow

The pipeline automates the following stages upon every code commit:

1.  **Checkout** → 2. **Security Scan (Trivy)** → 3. **Build** → 4. **Test** → 5. **Push to Docker Hub** → 6. **Deploy**

![Pipeline Success Graph](./images/01_pipeline_graph.png)
*Figure 1: Successful pipeline execution across all stages.*

### Deployment Console Output
Below is the live console output showing the Docker container being stopped, removed, and redeployed with the latest image.

![Deploy Console Logs](./images/03_deploy_logs.png)
*Figure 2: Deployment stage console output.*

##  Security Implementation (DevSecOps)

Security is shifted left in this pipeline. We integrate **Trivy** to scan for vulnerabilities before the image is ever pushed to production.

### 1. Vulnerability Database Download
![Trivy DB Download](./images/07_trivy_download.png)
*Figure 3: Trivy downloading the latest vulnerability database.*

### 2. File System Scan Summary
![Trivy Scan Summary](./images/05_trivy_summary.png)
*Figure 4: Summary of scanned files and detected vulnerabilities.*

### 3. Detailed CVE Reporting
![Trivy CVE Details](./images/06_trivy_cve.png)
*Figure 5: Detailed report on specific CVEs (e.g., Jackson-databind).*

## 🔐 Access Control & Infrastructure

### Role-Based Access Control (RBAC)
To ensure production stability, access is strictly controlled via Jenkins global roles.

![RBAC Configuration](./images/08_rbac_config.png)
*Figure 6: Configuring 'admin' vs 'intern' permissions.*

### Jenkins Backend Structure
Direct access to the Jenkins home directory to verify configurations and plugins.

![Jenkins Directory Structure](./images/09_jenkins_dir.png)
*Figure 7: Linux directory structure of Jenkins installation.*

### Artifact Registry
Verified successful push of Docker images to Docker Hub.

![Docker Hub Repositories](./images/10_docker_hub.png)
*Figure 8: Docker Hub repository showing latest image push.*

## 🚧 Troubleshooting & Real Problems Solved

Building a production pipeline comes with real-world hurdles. Here is how I solved them:

### 1. SSH Agent Connection Timeout
Initially, the Jenkins Master could not connect to the Agent node via SSH.

![SSH Connection Error](./images/02_ssh_error.png)
*Figure 9: Debugging SSH connection timeouts.*

**Solution:**
*   Verified Security Group rules (Port 22).
*   Checked SSH key permissions.
*   Confirmed Agent connectivity.

![SSH Agent Success](./images/04_ssh_success.png)
*Figure 10: Successful SSH agent connection log.*

### 2. Other Key Challenges

| Challenge | Solution |
| :--- | :--- |
| **EC2 Public IP Changed** | Implemented **Elastic IP** to ensure the GitHub Webhook URL remained static. |
| **Docker Permission Denied** | Added Jenkins user to Docker group: `sudo usermod -aG docker jenkins`. |
| **Workspace Permissions** | Fixed ownership: `sudo chown -R jenkins:jenkins /var/lib/jenkins`. |

## 💡 Key Architectural Learning: Master vs. Agent

One of the most critical lessons learned during this project was the importance of separating the **Control Plane** from the **Execution Environment**.

> **The Scenario:** I made changes to the main branch in GitHub, but the live website remained stable and running without interruption.
>
> **The Reason:**
> *   The **Master Node** only controls the pipeline logic.
> *   The **Agent Node** executes the build and hosts the running Docker container.
> *   Deployment is based on **built Docker images**, not directly on the GitHub branch code.
>
> **Conclusion:** Proper separation of roles ensures that pipeline orchestration does not interfere with application uptime.

## 🎯 Outcome

*   ✅ Fully automated deployment process.
*   ✅ Secure build environment with vulnerability scanning.
*   ✅ Scalable architecture ready for multiple agents.
*   ✅ Robust error handling and notification systems.

---
*Built by [Your Name] | DevOps Engineer*
```