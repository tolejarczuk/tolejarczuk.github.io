---
created: 2023-12-06 15:27
modified: Wednesday, 6th December 2023, 15:27:27
share: null
tags:
- devops
- ansible
- public
title: Ansible
date: 19-04-2024
---

# Ansible

### Ansible playbook syntax:

````bash
ansible-playbook ./playbooks/apt.yml -i ./inventory/hosts.ini
````

### Ansible global directory error on Windows

1. Unmont and mount C drive in WSL

````bash
sudo umount /mnt/c && sudo mount -t drvfs C: /mnt/c -o metadata
````

2. Go to ansible directory

````bash
cd /mnt/c/Users/Tom/ansible
````

3. Change permissions

````bash
sudo chmod o-w .
````

---

Last Modified: `=dateformat(this.file.mtime, "DDDD, HH:mm")`

#### Tags:

`=this.file.tags`

````dataview
List FROM #ansible 
````
