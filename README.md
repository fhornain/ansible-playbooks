Based on Ansible Technology, this project was initialy developed to collaborate with some E.U. institutions on industrialization/automation aspects.

Focus is currently on 
- Java 1.8.0
  - install-java-1.8.0-openjdk
- JBoss EAP 7
  - eap7-start
  - eap7-stop


# Preliminary tasks
------------

Install RHEL 8 
------------
All RHEL 8 information can be found at [http://access.redhat.com]:(https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/)

Register your server to EPEL repository
------------
[roo@localhost ~]# dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm

Install JBoss EAP 7 on your targeted system
------------
[roo@localhost ~]# yum groupinstall jboss-eap7

Include user in the Wheel group - sudoer
------------
[roo@localhost ~]# usermod -aG wheel user

Note : user is the username you are going to use to run your playbooks. 

Install Ansible
------------
[roo@localhost ~]# dnf install ansible 

Create SSH Public and Private Keys
------------
[user@localhost ~]$ ssh-keygen -t rsa
[user@localhost ~]$ cd ~
[user@localhost ~]$ cd .ssh
[user@localhost ~]$ cp id_rsa.pub authorized_keys
Or
[user@localhost ~]$ scp ~/.ssh/id_rsa.pub darvader@my_rhel8_server

Download the ansible-playbooks project
------------
[user@localhost ~]$ git clone https://github.com/fhornain/ansible-playbooks.git

Encrypt the sudo password with Ansible Vault 
------------
ansible-vault encrypt_string 'YOUR_SUDO_PASSWORD' --name 'ansible_sudo_pass'

This ansible command should then ask you a Vault password 
New Vault password: 
Confirm New Vault password: 

Then you should get the following output (here in anonymized format) 

ansible_sudo_pass: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx   
          xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx   
          xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx   
          xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx   
          xxxx


Copy the outcome of the previous step in the ansible-playbook/group_vars/all/all.yml file and save it
------------

---
ansible_sudo_pass: !vault |
          $ANSIBLE_VAULT;1.1;AES256
         xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
          xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
          xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
          xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
          xxxx

Add your targeted client - IP or Hostname/FQDN - in the hosts file
------------
For example :
[server]
10.10.10.10
myserver.mydomain.com

Compile the HelloWorld Java application
------------
[user@localhost ~]$ cd ansible-playbook/application/helloworld
[user@localhost helloworld]$ mvn clean package

Note : Do not forget to install maven if you want to compile the HelloWorld code.
[root@localhost ~]# dnf install maven

Execute the Ansible playbooks
------------
[user@localhost ~]$ ansible-playbook -i hosts --ask-vault-pass install-java-1.8.0-openjdk.yml

Command should ask you then you vault password

Note : If you did not create any SSH public and private keys
[user@localhost ~]$ ansible-playbook -i hosts -k --ask-vault-pass install-java-1.8.0-openjdk.yml

# Details
install-java-1.8.0-openjdk -> Install OpenJDK 1.8.0
eap7-start -> Start Red Hat JBoss EAP 7 
eap7-stop -> Stop Red Hat JBoss EAP 7

#License
[EUPL-1.2](./LICENSE.md)
https://joinup.ec.europa.eu/sites/default/files/custom-page/attachment/eupl_v1.2_en.pdf
