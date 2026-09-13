
```markdown
# Patch Compliance Report Using Ansible

**Author:** Aritra Mondal  
**Exclusive to:** Dabin Distro  

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
```
├── ansible.cfg
├── applied_patches.txt.j2
├── cred.yml
├── inventory
├── mail.html.j2
├── pending_patches.txt.j2
└── report.yml
```

---

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
```
Compliance = (Applied Patch Count) / (Applied + Pending Patch Count) * 100
```

---

## 🧠 Example Outputs

### Pending Patches
```
================================ controller =================================
Patches Pending: 3
Listing...
power-profiles-daemon/noble-updates 0.21-1ubuntu3 amd64 [upgradable from: 0.21-1ubuntu2]

================================ node3 =================================
Patches Pending: 17
Listing...
linux-aws/resolute-updates,resolute-security 7.0.0-1012.12 amd64 [upgradable from: 7.0.0-1006.6]
...
```

### Applied Patches
```
=============================== controller ===============================
Patches Applied: 212
2026-09-06 11:02:27 upgrade bsdutils:amd64 1:2.39.3-9ubuntu6.5 → 1:2.39.3-9ubuntu6.6
2026-09-06 11:02:28 upgrade coreutils:amd64 9.4-3ubuntu6.2 → 9.4-3ubuntu6.3
...
```

### Email Report
```
Patch Compliance Report | 13/09/2026-19:35:56

| Hostname   | IP Address       | Patch Pending | Patch Applied | Patch Compliance |
|------------|------------------|---------------|---------------|------------------|
| controller | 192.168.29.95    | 3             | 212           | 98 %             |
| node1      | 172.31.44.86     | 5             | 159           | 96 %             |
| node2      | 172.31.38.222    | 0             | 159           | 100 %            |
| node3      | 172.31.32.29     | 17            | 156           | 90 %             |
| node4      | 172.31.44.168    | 0             | 159           | 100 %            |

Attachments: applied_patches.txt, pending_patches.txt
```

---

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

---

## 📜 License
Exclusive to **Dabin Distro**. Internal use only.
```
