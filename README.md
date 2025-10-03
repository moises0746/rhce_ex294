# RHCE EX294 Exam Simulation (GitLab Markdown Format)

## RHEL-9 RHCE EXAM – EX294
**Duration:** 4 Hours  
**Total Marks:** 300  

---

## Instructions

**Control node:**
```
workstation.rhce-exam.ph
```

**Managed nodes:**
```
node1.rhce-exam.ph   (dev)
node2.rhce-exam.ph   (test)
node3.rhce-exam.ph   (prod)
node4.rhce-exam.ph   (prod)
node5.rhce-exam.ph   (balancers)
```

- The **prod group** is a member of the **webservers group**.

---

## Task Difficulty Breakdown

### 🟢 Easy (2–5 minutes each)
- Task 1: `ansible.cfg` + inventory setup
- Task 2: `repo.yml` (yum_repository)
- Task 3: `requirements.yml` (roles from Galaxy/tgz)
- Task 10: `hwreport.yml` (download, check empty → NONE)
- Task 11: `issue.yml` (/etc/issue per group)
- Task 12: `myhosts.yml` + `myhosts.j2` template (hostname, fqdn, ip)
- Task 15: Cron job (logger every 2 min)
- Task 16: Rekey with ansible-vault

### 🟡 Intermediate (8–15 minutes each)
- Task 4: Apache role with template (service + dynamic content)
- Task 5: `roles.yml` (balancer + phpinfo roles)
- Task 7: `users.yml` (loop, conditionals, user creation from file)
- Task 8: `webcontent.yml` (copy content, perms, SELinux, symlink)
- Task 9: `packages.yml` (install pkgs per group)
- Task 13: `webapp.yml` (handlers + index.html replacement)
- Task 16: LVM task (partition, PV, VG, LV, filesystem, mount, fstab)

### 🔴 Hard (15–25 minutes each)
- Task 6: Install collections from tarball + use system roles
- Task 17: Advanced Play (conditionals, facts, complex logic)
- Task 18+: SELinux booleans, firewalld rich rules, multi-play orchestration

---

## Strategy

- Clear all **easy tasks first** to secure ~120–150 points.  
- Then move to **intermediate tasks** (Apache role, packages, users).  
- Leave **hard tasks** last (collections, storage, SELinux/firewalld).  

---

# Exam Tasks

## 🟢 Task 1: Install and Configure Ansible
- Install required packages.  
- Create static inventory `/home/student/ansible/inventory`:  
  - node1 → dev group  
  - node2 → test group  
  - node3, node4 → prod group  
  - node5 → balancers group  
  - prod group is child of webservers group  
- Create `/home/student/ansible/ansible.cfg`:  
  - Define inventory file location  
  - Define roles path `/home/student/ansible/roles`  
  - Define collection path `/home/student/ansible/mycollection`

---

## 🟢 Task 2: Create `repo.yml`
- BaseOS repo:
  - name: baseos-internal
  - description: BaseOS Description
  - url: http://content-rhel9.rhce-exam.ph/rhel9/BaseOS
  - gpg enabled with key: http://content-rhel9.rhce-exam.ph/rhel9/RPM-GPG-KEY-redhat-release
- AppStream repo:
  - name: appstream-internal
  - description: App Description
  - url: http://content-rhel9.rhce-exam.ph/rhel9/AppStream
  - gpg enabled with same key above

---

## 🟢 Task 3: Requirements File
- Create `/home/student/ansible/roles` directory.  
- Create `requirements.yml` under roles directory.  
- Download roles:  
  - balancer → http://content-rhel9.rhce-exam.ph/rhel9/balancer.tgz  
  - phpinfo → http://content-rhel9.rhce-exam.ph/rhel9/phpinfo.tgz  

Example format:
```yaml
- src: https://github.com/geerlingguy/ansible-role-haproxy/archive/1.3.2.tar.gz
  name: balancer
- src: https://github.com/geerlingguy/ansible-role-php/archive/6.0.0.tar.gz
  name: phpinfo
```

---

## 🟡 Task 4: Apache Role
- Create offline role `apache` under roles directory.  
- Install and enable `httpd`.  
- Template (`template.j2`):
  ```
  Welcome to {{ ansible_fqdn }} on {{ ansible_default_ipv4.address }}
  ```
- Playbook: `apache_role.yml` → run on dev group.

---

## 🟡 Task 5: Roles Playbook
- Create `roles.yml` under `/home/admin/ansible/`.  
- Balancer play:
  - Run on balancers group.
  - Use balancer role to load-balance requests between webservers group.
- PHP Info play:
  - Run on webservers group.
  - Use phpinfo role to serve `/hello.php` → output: `Hello PHP World from <fqdn>`.

---

## 🟡 Task 6: Install Collections
- Install collections in local collection path.  
- Collections to install:  
  - http://server.rhce-exam.ph/role-collections/redhat-insights-1.0.7.tar.gz  
  - http://server.rhce-exam.ph/role-collections/redhat-rhel_system_roles-1.19.3.tar.gz  
  - http://server.rhce-exam.ph/role-collections/community-general-5.5.0.tar.gz  

---

## 🟡 Task 7: Packages Playbook
- Create `packages.yml` with separate plays:  
  - Install `vsftpd` and `mariadb-server` on dev and test group.  
  - Install group package `RPM Development Tools` on prod group.  
  - Update all packages on dev group.

---

## 🟡 Task 8: Web Content
- Playbook: `webcontent.yml` on dev group.  
- Requirements:
  - Directory `/devweb` owned by group `devops`.  
  - SELinux type: `httpd`.  
  - Permissions: `770` with SGID set.  
  - Create `index.html` with content `Development`.  
  - Symlink `/devweb` → `/var/www/html/devweb`.  

---

## 🟡 Task 9: Timesync System Role
- Playbook: `/home/admin/ansible/timesync.yml`.  
- Runs on all nodes.  
- Uses `timesync` role.  
- Provider: currently active NTP provider.  
- Server: `classroom.rhce-exam.ph`.  
- Enable `iburst` parameter.

---

## 🟢 Task 10: Hardware Report
- Download `hwreport.txt` from:  
  http://content-rhel9.rhce-exam.ph/rhel9/hwreport.empty → `/root/hwreport.txt`.  
- If empty, display `NONE`.  
- Playbook name: `hwreport.yml`.  

---

## 🟢 Task 11: Issue File Replacement
- Playbook: `issue.yml` on all nodes.  
- `/etc/issue` content per group:  
  - dev → `Development`  
  - test → `Test`  
  - prod → `Production`

---

## 🟡 Task 12: MyHosts Template
- Download template: http://content-rhel9.rhce-exam.ph/Rhce/myhosts.j2.  
- Gather node info (IP, FQDN, hostname).  
- Store results in `/etc/myhosts`.  
- Run playbook `hosts.yml` on dev group.  

Example expected file:
```
127.0.0.1 localhost.localdomain localhost
192.168.0.1 localhost.localdomain localhost
172.25.250.10 node1.rhce-exam.ph node1
172.25.250.11 node2.rhce-exam.ph node2
172.25.250.12 node3.rhce-exam.ph node3
172.25.250.13 node4.rhce-exam.ph node4
172.25.250.14 node5.rhce-exam.ph node5
```

---

## 🟢 Task 13: Vault File
- Create `vault.yml` with variables:  
  - `pw_developer: lamdev`  
  - `pw_manager: lammgr`  
- Encrypt with password: `P@sswOrd`.  
- Store password in `secret.txt`.

---

## 🔴 Task 14: User Management with Variables
- Download: http://content-rhel9.rhce-exam.ph/Rhce/user_list.yml.  
- Playbook: `users.yml`.  
- Use both `user_list.yml` and `vault.yml`.  
- Requirements:  
  - Group `opsdev` for users with job=developer.  
    - Password: `{{ pw_developer }}` (SHA512).  
    - Apply only to dev and test groups.  
  - Group `opsmgr` for users with job=manager.  
    - Password: `{{ pw_manager }}` (SHA512).  
    - Apply only to prod group.  
- Use `when` conditions for job role.

---

## 🟢 Task 15: Cronjob
- Playbook: `crontab.yml`.  
- Create cronjob for user student on all nodes.  
- Every 2 minutes run:  
  ```
  logger "EX294 in progress"
  ```

---

## 🟢 Task 16: Rekey Variable File
- Download: http://content-rhel9.rhce-exam.ph/Rhce/solaris.yml.  
- Rekey variable file:  
  - Old password: `cisco`  
  - New password: `redhat`  

---

## 🔴 Task 17: Logical Volume Creation
- Playbook: `/home/admin/ansible/lv.yml` on all nodes.  
- Requirements:  
  - VG: research  
  - LV: data, size 1500MB  
  - Format: ext4  
  - If not possible, display error and use 800MB.  
  - If VG missing, show error "volume group does not exist".  
  - Do **not** mount.  

---

## 🔴 Task 18: Alternative LV Creation
- Playbook: `lvm.yml` on all nodes.  
- Requirements:  
  - VG: research  
  - LV: data, size 1500MB  
  - If VG missing → debug msg "vg not found".  
  - If size insufficient → debug msg "Insufficient size of vg".  
  - If created → format as ext4.  
  - Do **not** mount.  
