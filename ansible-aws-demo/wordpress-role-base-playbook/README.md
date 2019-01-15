### Lets install Wordpress and use roles to split our playbooks.

- server
- php
- mysql
- wordpress

We will use `ansible-galaxy` command to create the role structure
Lets create a role directory and create all roles under those directory

```
mkdir roles
cd roles/
ansible-galaxy init server 
ansible-galaxy init php 
ansible-galaxy init mysql
ansible-galaxy init wordpress
```

