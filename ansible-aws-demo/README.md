# Introduction

Ansible is simple open source IT engine which automates application deployment, intra service orchestration, cloud provisioning and many other IT tools.
Ansible is easy to deploy because it does not use any agents or custom security infrastructure.

Ansible uses playbook to describe automation jobs, and playbook uses very simple language i.e. YAML 
Configuration management in terms of Ansible means that it maintains configuration of the product performance by keeping a record and updating detailed information which describes an enterprise’s hardware and software.

### How Ansible Works
Ansible works by connecting to your nodes and pushing out small programs, called "Ansible modules" to them. Ansible then executes these modules (over SSH by default), and removes them when finished.

![WorkFlow](https://github.com/sanjaynaikwadi/ansible/tree/master/ansible-aws-demo/Ansible_How_it_Works.png)

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
	- Inventory - List of hosts, there can be multiple inventory files.
	- Playbooks - Playbooks contain the steps which the user wants to execute on a particular machine. Playbooks are run sequentially.
	- Tasks - What task needs to be done
	- Handlers - What needs to be when task completes
	- Templates - Templates are simple text files that we can use in Ansible. J2 is extension of Jinja2 templating language that Ansible is using.
	- Variables - Variable in playbooks are very similar to using variables in any programming language.  
	- Roles - Roles are not playbooks. Roles are small functionality which can be independently used but have to be used within playbooks





