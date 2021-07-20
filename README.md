# Ansible-SSH-Key-Deploy
----------------------
## Description
This is a simple script to deploy SSH Keys with ansible. This role takes care of that. It creates a key locally on the ansible client (which it promptly deletes), and updates the Github API with the public key. The key in use is therefore only in existence for the life of the playbook run, and useless thereafter

## Pre-Requests
- Install Ansible on your Master Machine (localhost)
- Install pip, boto, boto3 and botocore 
- Create an IAM user role under your AWS account and please enter the values once the playbook running time or attach the role to ansible master server.

##### Ansible Modules used
- [yum](https://docs.ansible.com/ansible/2.9/modules/yum_module.html)
- [pip](https://docs.ansible.com/ansible/2.9/modules/pip_module.html)
- [ec2_key](https://docs.ansible.com/ansible/2.9/modules/ec2_key_module.html)
- [copy](https://docs.ansible.com/ansible/2.9/modules/copy_module.html)
## Behind the Playbook

I'm using AWS server as ansible master server. So, i've attached roll to the instance. Else we can add access_key and secret_key in playbook

For detailed code please check `ec2-key.yml`

```
---
- name: "Creating ssh key"
  hosts: localhost
  tasks:
    - name: "create a new ec2 key pair"
      ec2_key:  <---------------------------Module for create key pair
        region: ap-south-1
        name: keypair_name <---------------- mention name of the key
        state: present  
      register: key_pair
    - name: Save key   <--------- for saving the pem key in local machine.
      when: key_pair.changed == true
      copy:
        content: "{{ key_pair.key.private_key }}"
        dest: keypair_name.pem
        mode: 0400
```

