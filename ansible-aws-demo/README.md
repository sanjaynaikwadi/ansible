# Introduction

Ansible is simple open source IT engine which automates application deployment, intra service orchestration, cloud provisioning and many other IT tools.
Ansible is easy to deploy because it does not use any agents or custom security infrastructure.

Ansible uses playbook to describe automation jobs, and playbook uses very simple language i.e. YAML 
Configuration management in terms of Ansible means that it maintains configuration of the product performance by keeping a record and updating detailed information which describes an enterprise’s hardware and software.

### How Ansible Works
Ansible works by connecting to your nodes and pushing out small programs, called "Ansible modules" to them. Ansible then executes these modules (over SSH by default), and removes them when finished.

![WorkFlow](https://github.com/sanjaynaikwadi/ansible/blob/master/ansible-aws-demo/Ansible_How_it_Works.png)

### Installation Process
Mainly, there are two types of machines when we talk about deployment −

- *Control machine* − Machine from where we can manage other machines.

- *Remote machine* − Machines which are handled/controlled by control machine.

### Lets get started
- spin three nodes on AWS (I am using Ubuntu 16.4 machines for demo purpose), one will be your control node and other nodes we will use to deploy the softwares.

### Install Ansible via PIP
Login to one of your node and run the below command
```bash
sudo apt-get update
sudo apt-get install python-pip
sudo pip install ansible

ansible --version
```

### Things to remember
	* Inventory - List of hosts, there can be multiple inventory files.
	* Playbooks - Playbooks contain the steps which the user wants to execute on a particular machine. Playbooks are run sequentially.
	* Tasks - What task needs to be done
	* Handlers - What needs to be when task completes
	* Templates - Templates are simple text files that we can use in Ansible. J2 is extension of Jinja2 templating language that Ansible is using.
	* Variables - Variable in playbooks are very similar to using variables in any programming language.  
	* Roles - Roles are not playbooks. Roles are small functionality which can be independently used but have to be used within playbooks

### Lets test the conectivity from control machine to nodes
- Dump you key in one of the location to connect to nodes
- Default user used is ubuntu

Login to your control node and create the inventory file and ansible.cfg file, add your ipaddress of nodes 
```bash
ubuntu@ip-172-31-89-135:~$ mkdir ansible-demo/
cd ansible-demo

vi inventory.ini
[control]
127.0.0.1 ansible_connection=local

[web]
172.31.80.9

[db]
172.31.94.179

```
Lets now try a ping command and see the result

```
ubuntu@ip-172-31-89-135:~/ansible-demo$ ansible -m ping all  -i inventory.ini
172.31.94.179 | UNREACHABLE! => {
    "changed": false,
    "msg": "Failed to connect to the host via ssh: Permission denied (publickey).\r\n",
    "unreachable": true
}
172.31.80.9 | UNREACHABLE! => {
    "changed": false,
    "msg": "Failed to connect to the host via ssh: Permission denied (publickey).\r\n",
    "unreachable": true
}
127.0.0.1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

Ohhhh .... got the error .... yes as its aws where you need to specify the key file to connect to remote nodes, lets now add one file and mention all details below

```
ubuntu@ip-172-31-89-135:~/ansible-demo$ mkdir group_vars/
cd group_vars/

vi all

ansible_connection: ssh
ansible_ssh_private_key_file: /home/ubuntu/ansible-demo/devops.pem
ansible_user: ubuntu
ansible_python_interpreter: /usr/bin/python3

```
save the file and re-run the command from going back in demo directory 

```bash
ubuntu@ip-172-31-89-135:~/ansible-demo$ ansible -m ping all -i inventory.ini
127.0.0.1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
172.31.80.9 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
172.31.94.179 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

HOLAAA .... Connectivity is working and now you can create playbooks and play with it.

