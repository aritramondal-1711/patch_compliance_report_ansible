
# Patch Compliance Report Using Ansible

**Author:** Aritra Mondal  
**Exclusive to:** Dabian Distro  

---

## 📌 Purpose
This project automates **patch compliance reporting** across multiple Linux nodes using **Ansible**.  
- No agent installation required.  
- Relies only on **passwordless SSH** and **sudo privileges** for the `ubuntu` user.  
- Generates compliance metrics and emails a detailed report with attachments.

---

## ⚙️ Configuration
- **SSH Setup:** Passwordless SSH from controller → nodes using `ubuntu` user.  
- **Sudo Privilege:** Passwordless sudo configured for `ubuntu` user on all nodes.  
- **Software Requirements:**  
  - `ansible-core` installed on controller.  
  - `python3` installed on all nodes.  
- **Controller Setup:**  
  - `ansible.cfg` and `inventory` configured.  
  - Mail relay credentials stored securely in `cred.yml` (Ansible Vault).  

---

## 📂 File Structure

<img width="457" height="186" alt="image" src="https://github.com/user-attachments/assets/d7bdbec9-df2d-4463-99b3-8dbaca434deb" />



## 🔄 Workflow

### Play 1 – Node Operations
- Runs on all nodes (including controller).  
- Updates apt caches.  
- Collects patch data and sets custom facts:  
  - Pending patch count & list  
  - Applied patch count & list  

### Play 2 – Controller Operations
- Runs only on controller.  
- Renders templates:  
  - `mail.html.j2` → HTML mail body  
  - `applied_patches.txt.j2` → Applied patches list  
  - `pending_patches.txt.j2` → Pending patches list  
- Generates files:  
  - `/tmp/applied_patches.txt`  
  - `/tmp/pending_patches.txt`  
- Sends email with:  
  - HTML compliance table  
  - Two text attachments  

---

## 📊 Compliance Formula

<img width="388" height="87" alt="image" src="https://github.com/user-attachments/assets/8056f0d1-6cd2-4713-b27a-71896f60aee5" />

---

## 🧠 Example Outputs

### Pending Patches

<img width="1734" height="902" alt="image" src="https://github.com/user-attachments/assets/c6d4ac63-da83-4f29-847e-923affc86f0b" />


### Applied Patches

<img width="1734" height="902" alt="image" src="https://github.com/user-attachments/assets/a7bd2f4b-1e3f-4556-9d35-8ba491dec013" />


### Email Report

<img width="740" height="748" alt="image" src="https://github.com/user-attachments/assets/dc03a2d3-3257-4f21-a4ea-accf4491e07c" />


## 🧩 Architecture Diagram
```text
+-------------+        +------------------+
|  Controller | <----> |   Nodes (N)      |
|  (Ansible)  |        |  ubuntu user SSH |
+-------------+        +------------------+
       |
       v
+------------------+
| Compliance Report|
|  HTML + TXT Mail |
+------------------+
       |
       v
+------------------+
|   Email Relay    |
+------------------+
```

---

## ✅ Highlights
- **Agentless automation** using SSH and sudo.  
- **Centralized compliance reporting** with HTML and TXT outputs.  
- **Secure credential management** via Ansible Vault.  
- **Scalable design** — easily extendable to more nodes.  

---

## 🚀 Usage
1. Configure `ansible.cfg` and `inventory` on controller.  
2. Ensure SSH and sudo setup for `ubuntu` user.  
3. Encrypt mail credentials in `cred.yml` using Ansible Vault.  
4. Run the playbook:  
   ```bash
   ansible-playbook report.yml --ask-vault-pass
   ```
5. Check your email for compliance report and attachments.  

```
