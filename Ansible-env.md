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



Inventory

````bash
[myself:vars]
ansible_connection=local

[intranetweb]
192.168.186.132

[internetweb]
192.168.186.131
````
