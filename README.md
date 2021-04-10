# Ansible - working from home

A small project to automate the provisioning of a 2 small ARM servers (managed nodes) running Unifi Controller, Homebridge, Plex, etc.
My stack of choise is [Ansible](https://docs.ansible.com/ansible/latest/) with [Raspbian](https://github.com/homebridge/homebridge-raspbian-image/wiki/Getting-Started) and [Ubuntu](https://wiki.odroid.com/odroid-xu4/os_images/linux/ubuntu_5.4/ubuntu_5.4) for the servers.

Ansible is an agentless automation tool that you install on a control node. From the control node, Ansible manages machines and other devices remotely (by default, over the SSH protocol).

## 1. Installing Ansible

I'm using my Windows laptop as a Control Node with Ubuntu subsystem, as described in more depth [here](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installing-ansible-on-ubuntu). More on WSL in the last section.

```bash
sudo apt update
sudo apt install software-properties-common
sudo apt-add-repository --yes --update ppa:ansible/ansible
sudo apt install ansible
```

## 2. Registering managed nodes

These are the servers that need to be configured on the Controller Node. Ansible keeps an inventory of managed nodes in `/etc/ansible/hosts`. More on inventory [here](https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html#inventory).

```bash
sudo nano /etc/ansible/hosts
```

I edited this file by adding the IP addresses of the 2 servers at the begining of the file.

```
<USERNAME>@<IP-ADDRESS>
<USERNAME>@<IP-ADDRESS>
# This is the default ansible 'hosts' file.
#
# It should live in /etc/ansible/hosts
```

## 3. Generating public key on the managed node

This will enable seamless login from my laptop to the servers. More [here](https://ubuntu.com/tutorials/ssh-keygen-on-windows#3-key-generation-with-ubuntu-on-wsl)

```bash
sudo apt install openssh-client
```

Then I was able to generate a public key. I chose not to use a passphrase and I saved the path of the generated certificate, usually linked to: `~/.ssh/id_rsa.pub`.

```bash
ssh-keygen -t rsa
```

## 4. Copying public key to the managed nodes (servers)

 I will nedd to replace ``<USERNAME>@<IP-ADDRESS>`` for each server. This command will ask for the password.

```bash
cat ~/.ssh/id_rsa.pub | ssh <USERNAME>@<IP-ADDRESS> 'mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys'
```

To test that all went well, I use ssh to login to the servers, without using a password:

```bash
ssh <USERNAME>@<IP-ADDRESS>
```


## 6. Confirming your installation

Running the ping command on all the inventory should return a JSON message for each host.
```bash
ansible all -m ping
```
```json
<USERNAME>@<IP-ADDRESS> | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
<USERNAME>@<IP-ADDRESS> | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
```

## 6. Working on Windows

[WSL 2](https://aka.ms/wsl)
