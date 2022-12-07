# Tasks performed

# 1st lab
1. Install ansible and the infraestructure. Host linux and 2 virtual cisco routers in a local network(host only adapter)

2. Introduction to obtain some info from routers through commands. Setting the inventory and hosts file. 

3. Solved issue with ssh through 'ansible-paramiko' to perform operations. Updating ansible and avoid using sha2_algorithms through variable in 'defaults' in ansible.cfg

# 2nd lab

## Task 1. Simple playbook

Create playbook that will configure basic device settings:

    - hostname
    - ntp servers
    - syslog servers
    - service password-encryption

## Task 2. Variables

Create file group_vars/all.yaml

Write there variables for:

    - ntp servers
    - syslog servers

Create file host_vars/\<name_of_your_device\>.yaml

Write there variables for:

    - hostname

## Task 3. Playbook with variables

Put variables into your playbook from task 1. Apply plays to group "routers", can be 1 or 2 devices in group, based on your resources.

## Task 4. Make your playbook idemponent

If you didn't do any changes with variables or playbook, command

    ansible-playbook lab02.yaml

shouldn't do any changes.

Most probably you should adjust commands in playbook to make it idemponent.


# Lab 3

## Task 1. A bit more advanced playbook

Create playbook that will configure basic device settings for single router:

    - hostname
    - ntp servers
    - syslog servers
    - service password-encryption

## Task 2. Save config if needed

If some changes were done by playbook - save config. Do nothing if no changes were made.

## Task 3. Backup your config

Create config backup before applying any changes to the device.

Backup files should be in folder "ios_backups" with names "\<device_hostname\>.backup"

## Task 4. Create access list for vty lines

Create extended access-list 101 to allow ssh connections to VTY lines.

Ansible server IP should be permitted in the first line of this acl. Ansible server IP should come from variables, destination IP also should come from variables, best options would be to reuse ansible_host from inventory.

Don't allow any changes in access list if the first line doesn't allow connections from Ansible server. In this case, just print a message with explanation. Example:

    Acl:
      - deny ip any any
      - permit tcp host {{ ansible_server }} host {{ ansible_host }} eq 22
    Msg:
      Skipping ACL change because of possible ssh lock.

## Task 5. Collect additional info about your devices

With the help of ios_facts module create a report about your network devices. Reports should be in "reports/\<device_name\>.info".

Content of report:

    Hostname: XXX
    Model: XXX
    IOS type: XXX
    IOS version: XXX
    Serial number: XXX

## Expected result

If you didn't do any changes with variables or playbook, command

    ansible-playbook lab03.yaml

shouldn't do any changes.

Most probably you should adjust commands in playbook to make it idemponent.


# Lab 4

In this lab we will configure OSPF on a pair of routers.

## Task 1. Basic OSPF setup

Create Jinja2 template that will configure OSPF on routers. Put management interface to area 0.

Create a task in playbook that will configure routers using created Jinja2 template:

    ios_config:
      src: ....j2

## Task 2. Create Loopback interfaces

Create `loopbacks` variable that describes additional interfaces for each router:

    loopbacks:
      lo0:
        ip: 10.1.0.1/26
        description: Some text  # Optional
        ospf_area: 2  # Optional. Don't enable OSPF if missing
      lo1:
        ip: 10.2.0.1/27
      lo2:
        ip: 10.3.0.1/22
        ospf_area: 32
      lo3:
        ...
      lo4:
        ...

Based on provided info, create two Jinja2 templates:
  - interface configuration
  - `network` statements under `router ospf` for OSPF-enabled interfaces

Create 2 tasks in playbook that will configure routers using created Jinja2 templates.

## Task 3. Setup OSPF authentication

Create Jinja2 template that will configure md5 OSPF authentication on management interface.

Use Ansible Vault to store the password in your repo. Update your `ansible.cfg` with vault password file location. Vault password file should be outside of your repo.

Create a task in playbook that will configure router using created Jinja2 template.

## Task 4. Print some OSPF info

Parse the output of `show ip ospf neighbor` and print all OSPF neighbors IDs.

Parse the output of `show ip route ospf` and print the number of OSPF learned routes.

## Expected result

All configuration should be done with single command without any parameters:

    ansible-playbook lab04.yaml
