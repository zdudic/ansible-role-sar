Role Name: sar
==============
This is Ansible role for managing System Activity Report. 
The role supports next use cases:

1. Define a host (use inventory_hostname) on which this role doesn't manage sar. Maybe you have some reason for this.   
2. Define a host group on which this role doesn't manage sar. Think of this as all hosts for a project that doesn't need sar.  
3. If there is no host or host-group based use case, then role does default sar configuration, default is based on a distribution.  

Requirements
------------
None

Role Variables
--------------

1. Default variables (defaults/main.yml):

```
# default host group
host_group: "default_host_group"

# default sar config for default host group, 
# this is naturally "yes" since you want the role to manage sar
default_host_group:
  sar_cfg: "yes"

```

2. Your host/host-group based sar_cfg variable (vars/main.yml)

```
# Here, likely you'll define hosts/host-groups that don't need sar to be managed

# Define your host (use inventory_hostname) sar_cfg variable
inventory_hostname_1:
  sar_cfg: "N"

# Define your host group sar_cfg variable
host_group_name_1:
  sar_cfg: "N"

# Supported values:
# For positive: [Yy][Ee][Ss], Y, y
# For negative: [Nn][Oo], N, n

# Additionally, this var defines list of supported distribution:
supported_distro_list:
  - "OracleLinux"
  - "Oracle"
  - "Solaris"
```

3. Dymanic variables (vars/main.yml)

```
# These are variables that are dynamically loaded in the next order (first come, first served): 
# (1) host based
# (2) host-group based
# (3) Ansible distribution

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
hostname-1 host_group=group1
# this host doesn't have host_group variable, hence default host group is used
hostname-2 
```

```
---
- name: SAR Playbook 
  hosts: all  
  gather_facts: no
  become: yes
  roles:
    - role: sar
...
```

Maintenance and support
-----------------------

```
1. To disable sar management by this role, create host/host-group based sar_cfg var with value "no". 

2. For hostname, use Ansible var {{ inventory_hostname }}

3. If you have host/host-group based sar config, review files vars/inventory_hostname_example.yml and 
   vars/hostgroup_name_example.yml, and create something like that for your hosts. 

4. To add support for new distribution:
4.1 Add distro name to supported_distro_list var (list type)
4.2 Create vars/{{ ansible_distribution }}.yml with desired sar vars (as example, see yml files for present distros)   
4.3 In tasks/sar-yes.yml, you'll need to add new {{ ansible_distribution }} in the task "Manage sar package (no OL5, Solaris10)"

5. Note about task "Manage sar package (no OL5, Solaris10)" in sar-yes.yml. 
   I've excluded OL5 and Solaris 10, since module 'package' likely fails on them. 
```

Author Information
------------------
Created by Zarko D.

License
---------
Hello! I’ve created this, and placed it in the Public domain. 
There is no ownership such as copyright, trademark, patent, and there is no warranty. 
Thank you.
