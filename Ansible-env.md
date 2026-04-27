# Ansible

Here are some things to consider when working with an ansbile environment.

## SSH keys

The key principle of Ansible is a connection without a password. When the connection always fail, take a look at the keys. Keys are generated as here.

````bash
ssh-keygen -t rsa -b 4096
````

Click enter when prompted for a password.

The next step is to copy the key to the targed (managed) host.

````bash
ssh-copy-id <user>@<IP>
ssh-copy-id <user>@<IP>
````

Test the connection with:

````bash
ssh <user>@<IP>
````



## Inventory

Check if the hosts are in the file. There are two ways:

- FQDN
- IP

Use group names or group nasting for more efficient playbooks.

````bash
[myself:vars]
ansible_connection=local

[intranetweb]
192.168.186.132

[internetweb]
192.168.186.131
````

## Ansible.cfg

Ansible.cfg is the configuration file for Ansible. Here are some base values that are used for later playbooks.

````bash
[defaults]
inventory = ./inventory
remote_user = <username>

[privilege_escalation]
become = false
become_method = sudo
become_user = root
become_ask_pass = false
````

## Playbooks

There are many different ways to write playbooks. The most basic playbook is the ping-all.yml to test a connection to a managed host.
````bash
---
- name: Validate inventory hosts
  hosts: myself, intranetweb, internetweb
  gather_facts: false

  tasks:

    - name: Ping internetweb
      ansible.builtin.ping:
````

Here for example is a playbook with the installation of an apache webserver:
````bash
---
- name: Install Apache Webserver
  hosts: web
  become: yes
  tasks:

    - name: Enable Repos on RHEL
      ansible.builtin.command:
        cmd: dnf config-manager --set-enabled baseos appstream
      when: ansible_os_family == "RedHat"

    - name: Install Apache on RHEL/CentOS
      ansible.builtin.dnf:
        name: httpd
        state: present
        update_cache: yes
      when: ansible_os_family == "RedHat"

    - name: Install Apache on Ubuntu/Debian
      ansible.builtin.apt:
        name: apache2
        state: present
        update_cache: yes
      when: ansible_os_family == "Debian"

    - name: Start and enable Apache on RHEL
      ansible.builtin.service:
        name: httpd
        state: started
        enabled: yes
      when: ansible_os_family == "RedHat"

    - name: Start and enable Apache on Ubuntu
      ansible.builtin.service:
        name: apache2
        state: started
        enabled: yes
      when: ansible_os_family == "Debian"
````

## Troubleshoot

There may be many things why Ansible won´t work:

- no SSH key
- SSH service not enabled
- the wrong key is used for connection
- the wrong user is used
- playbook errors
