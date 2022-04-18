Role Name: sar
==============
This is Ansible role for managing System Activity Report. 
The role supports next use cases:

1. Define a host (use inventory_hostname) on which this role doesn't manage sar (maybe there is some reason for this).   
2. Define a host group on which this role doesn't manage sar (think of this as all hosts for a project that doesn't need sar).  
3. If there is no host or host-group based use case, then role does default sar configuration, default is based on os-family.  

Min Ansible version
-------------------
2.10.7

Requirements
------------
None

Role Variables
--------------

1. Default variables (defaults/main.yml):

```
# default sar configuration is naturally "yes"
sar_cfg: "yes"
```

2. The variable {{ inventory_hostname }} is used if there is specific sar configuration for that host. 

3. The variable {{ host_group }} is used if there is specific sar configuration for that group of hosts. 
   This variable can be added in the inventory, see inventory example below.  

4. Dymanic variables (vars/main.yml)

```
# These are variables that are dynamically loaded in the next order (first come, first served): 
# (1) host based
# (2) host-group based
# (3) Ansible os-family

# Example for a Linux host
sar_package: "sysstat"
sar_cron_file: "/etc/cron.d/sysstat"
sar_log_path: "/var/log/sa"
sar_log_path_mode: "0755"
sar_owner: "root"
sar_group: "root"
sar_cron_content: |
  #
  # {{ inventory_hostname }}
  #
  15 * * * * root /usr/lib64/sa/sa1 1 1
  53 23 * * * root /usr/lib64/sa/sa2 -A
```

Dependencies
------------
None

Example Inventory and Playbook
-----------------------------

```
# this is just example
[all:vars]
ansible_ssh_common_args='-o StrictHostKeyChecking=no'
ansible_user=some-user
ansible_ssh_private_key_file='/home/some-user/.ssh/id_rsa'
# maybe also
#ansible_ssh_pass=some-password

[all]
# this host is member of some host group, there is variable host_group. 
hostname-one host_group=group-one
# this host doesn't have host_group variable
hostname-two
```

```
---
- name: SAR Playbook 
  hosts: all  
  gather_facts: no
  become: yes
  roles:
    - role: ansible-role-sar
...
```

Maintenance and support
-----------------------

```
1. To disable sar management by this role, in vars, create inventory_hostname.yml 
   or host-group.yml, and define sar_cfg as "No". 

2. For hostname, use Ansible var {{ inventory_hostname }}

3. If you have host/host-group based sar config, review files vars/inventory_hostname_example.yml and 
   vars/hostgroup_name_example.yml, and create something like that for your hosts. 
```

Author Information
------------------
Created by Zarko D.

License
---------
Hello! I’ve created this, and placed it in the Public domain. 
There is no ownership such as copyright, trademark, patent, and there is no warranty. 
Thank you.
