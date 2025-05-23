# Jenkins + Maven + Ansible CI/CD Pipeline

## 📌 Overview

This project demonstrates a complete CI/CD pipeline using Jenkins, Maven, and Ansible:

* Set up a Jenkins job to build a Maven project from a Git repository.
* Archive the generated JAR file as a build artifact.
* Deploy the artifact to a target location using Ansible.
* Validate the successful deployment of the artifact.

## ✅ Prerequisites

Before beginning, ensure the following are installed and configured:

* **Jenkins** (locally or cloud)
* **Maven Project** (e.g., `HelloMaven`) in a Git repository
* **Git** (with repo access from Jenkins)
* **Ansible** (on Jenkins server or control machine with `hosts.ini` inventory)

## 🔧 Step 1: Preparing the Maven Project

1. Ensure your Maven project is under version control:

```bash
cd path/to/HelloMaven
git init
git add .
git commit -m "Initial commit of HelloMaven project"
git remote add origin https://github.com/yourusername/HelloMaven.git
git push -u origin main
```

2. Maven project structure should look like:

```
HelloMaven/
├── pom.xml
└── src
    ├── main/java/com/example/App.java
    └── test/java/com/example/AppTest.java
```

## 🛠️ Step 2: Configuring Jenkins to Build the Maven Project

### A. Create a Jenkins Job

1. Open `http://localhost:8080`
2. Click **New Item** > Enter "HelloMaven-CI" > Select **Freestyle project** > Click **OK**

### B. Configure Source Code Management (SCM)

1. Select **Git**
2. Enter repository URL (e.g., `https://github.com/yourusername/HelloMaven.git`)
3. Add credentials if private
4. Branch Specifier: `*/main` or `*/master`

### C. Add a Maven Build Step

1. Scroll to **Build** > **Add build step** > `Invoke top-level Maven targets`
2. Goals: `clean package`
3. Leave POM file path default if at root

## 📦 Step 3: Archiving the Artifact

1. Go to **Post-build Actions**
2. Add **Archive the artifacts**
3. Files to archive: `target/*.jar`

## 🚀 Step 4: Integrating Ansible Deployment in Jenkins

1. Add another **Post-build Action**: `Execute shell`
2. Shell Command:

```bash
ansible-playbook -i /path/to/hosts.ini /path/to/deploy.yml
```

3. Adjust paths to your actual inventory and playbook files
4. Save the Jenkins job

## 📜 Step 5: Writing an Ansible Playbook for Deployment

### A. Create an Inventory File (hosts.ini)

```ini
[local]
localhost ansible_connection=local
```

### B. Create a Deployment Playbook (deploy.yml)

```yaml
---
- name: Deploy Maven Artifact
  hosts: local
  become: yes
  tasks:
    - name: Copy the artifact to the deployment directory
      copy:
        src: "/var/lib/jenkins/workspace/HelloMaven-CI/target/HelloMaven-1.0-SNAPSHOT.jar"
        dest: "/opt/deployment/HelloMaven.jar"
```

* Make sure the destination directory exists or create it with correct permissions.

## 🧪 Step 6: Testing the Complete Pipeline

1. Trigger a Jenkins build:

   * Go to **HelloMaven-CI** job and click **Build Now**
   * Monitor the **Console Output**
   * Confirm:

     * Maven build success
     * Artifact archived
     * Ansible playbook executed

2. Verify Deployment:

```bash
ls -l /opt/deployment/
```

* Confirm `HelloMaven.jar` is present

---

✅ You’ve now built a full CI/CD pipeline with Jenkins, Maven, and Ansible!
