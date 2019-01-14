### Pre-requistes
- Assuming you have all nodes connected and your able to ping pong the nodes

### What we will do
- Create a index.html and copy to destination node
- Install apache latest apache package 
- Once file is copied restart the apache service to serve the new index.html file

### Playbook, create a directory as playbooks and create the below files. 

- vim install_apache.yml

```
---

- name: Install apache packages
  hosts: web
  become: true
  tasks:
    - name: Install apache packages
      apt:
        name:
        - apache2
        update_cache: yes
        state: latest

    - name: copy default index.html to /var/www/html
      copy:
        src: index.html
        dest: /var/www/html/
      notify:
        - restart apache

  handlers:
    - name: restart apache
      service: name=apache2 state=restarted
```

- vim index.html 

```
Hola Ansible is working !!!

```

- Make sure the index.html should be in same directory of playbook

### Lets run the playbook
```
ansible-playbook playbook/install_apache.yml
```

### Output of playbook
```
ubuntu@ip-172-31-89-135:~/ansible-demo$ ansible-playbook playbooks/install_apache.yml

PLAY [Install apache packages] ******************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************
ok: [172.31.80.9]

TASK [Install apache packages] ******************************************************************************************************************************
changed: [172.31.80.9]

TASK [copy default index.html to /var/www/html] *************************************************************************************************************
ok: [172.31.80.9]

PLAY RECAP **************************************************************************************************************************************************
172.31.80.9                : ok=3    changed=1    unreachable=0    failed=0
```

- Once you have successfully run the play book, browse the web interface and you will see the content of index.html which you created earlier.

