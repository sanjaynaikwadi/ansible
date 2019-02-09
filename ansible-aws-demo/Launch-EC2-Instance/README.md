# How to launch EC2 Instance with Ansible

### Pre-requisite
- Make sure you have Ansible and boto installed in your Mac/Laptop
- Boto will hold the Access Key and Secret Key details, which is required by Ansible

### Let create a boto file
```
[Credentials]
aws_access_key_id = YOUR ACCESS KEY
aws_secret_access_key = YOUR SECRET KEY
```

### Lets create inventory file
```
vim hosts

[local]
localhost

[webserver]

```

### Make sure you have your private key file in same directory to make SSH connection

### Create a file to launch ec2 instances

<details><summary>show</summary>
<p>

```YAML
---
  - name: Provision an EC2 Instance
    hosts: local
    connection: local
    gather_facts: False
    tags: provisioning
    # Necessary Variables for creating/provisioning the EC2 Instance
    vars:
      instance_type: t2.micro # Select which type of Instance you need
      security_group: webserver # Set the Security Group name
      image: ami-0f9cf087c1f27d9b1 # Change the AMI, from which you want to launch the server ( For testing I am using Ubuntu AMI)
      region: us-east-1 # Selet the Region where you want to launch the instance
      keypair: devops-ansible # Make sure you have crated the keypair on aws and your passing here private key name
      count: 1  # Number of instances to launch
      volumes:
              - device_name: /dev/sda1
                volume_size: 40
                volume_type: gp2  ## you can specify here different types

    # Task that will be used to Launch/Create an EC2 Instance
    tasks:

      - name: Create a security group
        local_action:
          module: ec2_group
          name: "{{ security_group }}"
          description: Security Group for webserver Servers
          region: "{{ region }}"
          rules:
            - proto: tcp
              from_port: 22
              to_port: 22
              cidr_ip: 0.0.0.0/0
            - proto: tcp
              from_port: 80
              to_port: 80
              cidr_ip: 0.0.0.0/0
          rules_egress:
            - proto: all
              cidr_ip: 0.0.0.0/0

      - name: Launch the new EC2 Instance
        local_action: ec2
                      group={{ security_group }}
                      instance_type={{ instance_type}}
                      image={{ image }}
                      wait=true
                      region={{ region }}
                      keypair={{ keypair }}
                      count={{count}}
                      volumes={{volumes}}
        register: ec2

      - name: Add the newly created EC2 instance(s) to the local host group (located inside the directory)
        local_action: lineinfile
                      dest="./hosts"
                      regexp={{ item.public_ip }}
                      insertafter="[webserver]" line={{ item.public_ip }}
        with_items: "{{ ec2.instances }}"

      - name: Wait for SSH to come up
        local_action: wait_for
                      host={{ item.public_ip }}
                      port=22
                      state=started
        with_items: "{{ ec2.instances }}"

      - name: Add tag to Instance(s)
        local_action: ec2_tag resource={{ item.id }} region={{ region }} state=present
        with_items: "{{ ec2.instances }}"
        args:
          tags:
            Name: webserver

```

</p>
</details>



