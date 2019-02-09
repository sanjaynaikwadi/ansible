# How to launch EC2 Instance with Ansible

### Pre-requisite
- Make sure you have Ansible and boto installed in your Mac/Laptop
- Boto will hold the Access Key and Secret Key details, which is required by Ansible

### Let create a boto file

```bash
vim ~/.boto

[Credentials]
aws_access_key_id = YOUR ACCESS KEY
aws_secret_access_key = YOUR SECRET KEY
```

### Lets create inventory file
This will be needed for Ansible to read the inventory file while launching the ec2-instances, also a new server which will be launched under
group name called `webserver`.

```bash
vim hosts

[local]
localhost

[webserver]

```

### Make sure you have your private key file in same directory to make SSH connection
I am using key name as devops-ansible, this key name might be different in your enviornment.

### Create a file to launch ec2 instances
Sample code where you can launch t2.micro instance of Ubuntu. 

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


### Let run the playbook

```bash
ansible-playbook -i hosts ec2_launch.yml

```

You will see the output something like this, and you can verify the public ip would be added in your host file

```bash

PLAY [Provision an EC2 Instance] ****************************************************************************************************************************

TASK [Create a security group] ******************************************************************************************************************************
ok: [localhost -> localhost]

TASK [Launch the new EC2 Instance] **************************************************************************************************************************
changed: [localhost -> localhost]

TASK [Add the newly created EC2 instance(s) to the local host group (located inside the directory)] *********************************************************
changed: [localhost -> localhost] => (item={u'ramdisk': None, u'kernel': None, u'root_device_type': u'ebs', u'private_dns_name': u'ip-172-31-87-188.ec2.internal', u'block_device_mapping': {u'/dev/sda1': {u'status': u'attached', u'delete_on_termination': False, u'volume_id': u'vol-053f496491bfe5f37'}}, u'key_name': u'devops-ansible', u'public_ip': u'3.85.47.231', u'image_id': u'ami-0f9cf087c1f27d9b1', u'tenancy': u'default', u'private_ip': u'172.31.87.188', u'groups': {u'sg-0e40ed94232568846': u'webserver'}, u'public_dns_name': u'ec2-3-85-47-231.compute-1.amazonaws.com', u'state_code': 16, u'id': u'i-06e6b76acb57407a3', u'tags': {}, u'placement': u'us-east-1d', u'ami_launch_index': u'0', u'dns_name': u'ec2-3-85-47-231.compute-1.amazonaws.com', u'region': u'us-east-1', u'ebs_optimized': False, u'launch_time': u'2019-02-09T12:22:59.000Z', u'instance_type': u't2.micro', u'state': u'running', u'architecture': u'x86_64', u'hypervisor': u'xen', u'virtualization_type': u'hvm', u'root_device_name': u'/dev/sda1'})

TASK [Wait for SSH to come up] ******************************************************************************************************************************
ok: [localhost -> localhost] => (item={u'ramdisk': None, u'kernel': None, u'root_device_type': u'ebs', u'private_dns_name': u'ip-172-31-87-188.ec2.internal', u'block_device_mapping': {u'/dev/sda1': {u'status': u'attached', u'delete_on_termination': False, u'volume_id': u'vol-053f496491bfe5f37'}}, u'key_name': u'devops-ansible', u'public_ip': u'3.85.47.231', u'image_id': u'ami-0f9cf087c1f27d9b1', u'tenancy': u'default', u'private_ip': u'172.31.87.188', u'groups': {u'sg-0e40ed94232568846': u'webserver'}, u'public_dns_name': u'ec2-3-85-47-231.compute-1.amazonaws.com', u'state_code': 16, u'id': u'i-06e6b76acb57407a3', u'tags': {}, u'placement': u'us-east-1d', u'ami_launch_index': u'0', u'dns_name': u'ec2-3-85-47-231.compute-1.amazonaws.com', u'region': u'us-east-1', u'ebs_optimized': False, u'launch_time': u'2019-02-09T12:22:59.000Z', u'instance_type': u't2.micro', u'state': u'running', u'architecture': u'x86_64', u'hypervisor': u'xen', u'virtualization_type': u'hvm', u'root_device_name': u'/dev/sda1'})

TASK [Add tag to Instance(s)] *******************************************************************************************************************************
changed: [localhost -> localhost] => (item={u'ramdisk': None, u'kernel': None, u'root_device_type': u'ebs', u'private_dns_name': u'ip-172-31-87-188.ec2.internal', u'block_device_mapping': {u'/dev/sda1': {u'status': u'attached', u'delete_on_termination': False, u'volume_id': u'vol-053f496491bfe5f37'}}, u'key_name': u'devops-ansible', u'public_ip': u'3.85.47.231', u'image_id': u'ami-0f9cf087c1f27d9b1', u'tenancy': u'default', u'private_ip': u'172.31.87.188', u'groups': {u'sg-0e40ed94232568846': u'webserver'}, u'public_dns_name': u'ec2-3-85-47-231.compute-1.amazonaws.com', u'state_code': 16, u'id': u'i-06e6b76acb57407a3', u'tags': {}, u'placement': u'us-east-1d', u'ami_launch_index': u'0', u'dns_name': u'ec2-3-85-47-231.compute-1.amazonaws.com', u'region': u'us-east-1', u'ebs_optimized': False, u'launch_time': u'2019-02-09T12:22:59.000Z', u'instance_type': u't2.micro', u'state': u'running', u'architecture': u'x86_64', u'hypervisor': u'xen', u'virtualization_type': u'hvm', u'root_device_name': u'/dev/sda1'})

PLAY RECAP **************************************************************************************************************************************************
localhost                  : ok=5    changed=3    unreachable=0    failed=0

```

#### Hola your ec2-instance is ready NOW !!!


## Destroy you Setup !!! < Do not do this in Production > 
This will help you to tear down your test setup which you created for testing.

<details><summary>show</summary>
<p>

This will match the TAG set as Name:webserver and terminate those instances.

```YAML
---

- hosts: local
  connection: local
  vars:
    region: us-east-1
  tasks:
    - name: Gather EC2 facts
      ec2_instance_facts:
        region: "{{ region }}"
        filters:
          "tag:Name": "webserver"
      register: ec2
    - debug: var=ec2

    - name: Terminate EC2 Instance(s)
      ec2:
        instance_ids: '{{ item.instance_id }}'
        state: absent
        region: "{{ region }}"
      with_items: "{{ ec2.instances }}"

```

</p>
</details>


