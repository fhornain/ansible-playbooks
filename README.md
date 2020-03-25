Based on Ansible Technology, this project was initialy developed to collaborate with some E.U. institutions on industrialization/automation aspects.

Focus is currently on 
- Java 1.8.0
  - install-java-1.8.0-openjdk
- JBoss EAP 7
  - eap-7-start
  - eap-7-stop
  - eap-7-deploy-application
  - eap-7-undeploy-application
  - eap-7-enable-datasource
  - eap-7-disable-datasource
  - eap-7-create-admin
  - eap-7-delete-admin
  - eap-7-product-info


# Preliminary tasks
------------

Install RHEL 8 
------------
All RHEL 8 information can be found at [http://access.redhat.com](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/)

Register your server to EPEL repository
------------
```
[root@localhost ~]# dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
```

Install JBoss EAP 7 on your targeted system
------------
```
[root@localhost ~]# yum groupinstall jboss-eap7
```

**Note**
Here in this exercice, JBoss EAP 7 is in default mode - standalone.


Set a Admin user in your JBoss EAP 7 configuration
------------
```
[root@localhost ~]# /opt/rh/eap7/root/usr/share/wildfly/bin/add-user.sh
```

Change all IP 127.0.0.1 in 0.0.0.0
------------
```
[root@localhost ~]# sed -i -e 's,127.0.0.1,0.0.0.0,g' /opt/rh/eap7/root/usr/share/wildfly/standalone/configuration/standalone.xml 
```

**Note**
This command indicate the EAP 7 instance to listen to all interace.
Thing not really recommended to do in production environment. 


Include user in the Wheel group - sudoer
------------
```
[root@localhost ~]# usermod -aG wheel user
```

**Note**
user is the username you are going to use to run your playbooks. 

Install Ansible
------------
```
[root@localhost ~]# dnf install ansible 
```

Create SSH Public and Private Keys
------------
```
[user@localhost ~]$ ssh-keygen -t rsa
[user@localhost ~]$ cd ~
[user@localhost ~]$ cd .ssh
[user@localhost ~]$ cp id_rsa.pub authorized_keys
```
or
```
[user@localhost ~]$ scp ~/.ssh/id_rsa.pub darvader@my_rhel8_server
```

Download the ansible-playbooks project
------------
```
[user@localhost ~]$ git clone https://github.com/fhornain/ansible-playbooks.git
```

Encrypt the sudo password with Ansible Vault 
------------
```
[user@localhost ~]$ ansible-vault encrypt_string 'YOUR_SUDO_PASSWORD' --name 'ansible_sudo_pass'
```

This ansible command should then ask you a Vault password 
New Vault password: 
Confirm New Vault password: 

Then you should get the following output (here in anonymized format) 
```
ansible_sudo_pass: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx   
          xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx   
          xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx   
          xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx   
          xxxx
```

Copy the outcome of the previous step in the ansible-playbook/group_vars/all/all.yml file and save it
------------
```
---
ansible_sudo_pass: !vault |
          $ANSIBLE_VAULT;1.1;AES256
         xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
          xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
          xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
          xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
          xxxx
```

Add your targeted client - IP or Hostname/FQDN - in the hosts file
------------
For example :
```
[server]
10.10.10.10
myserver.mydomain.com
```

Encrypt the jboss admin password with Ansible Vault 
------------
```
[user@localhost ~]$ ansible-vault encrypt_string 'YOUR_SUDO_PASSWORD' --name 'jboss_admin_password'
```

This ansible command should then ask you a Vault password
New Vault password:
Confirm New Vault password:

Then you should get the following output (here in anonymized format)
```
jboss_admin_password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
          xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
          xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
          xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
          xxxx
```

Copy the outcome of the previous step in the ansible-playbook/group_vars/all/all.yml file and save it
------------
```
---
jboss_admin_password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
         xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
          xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
          xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
          xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
          xxxx
```



Encrypt the jboss test user (with admin rights) password with Ansible Vault
------------
```
[user@localhost ~]$ ansible-vault encrypt_string 'YOUR_SUDO_PASSWORD' --name 'jboss_user_password'
```

This ansible command should then ask you a Vault password
New Vault password:
Confirm New Vault password:

Then you should get the following output (here in anonymized format)
```
jboss_user_password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
          xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
          xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
          xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
          xxxx
```

Copy the outcome of the previous step in the ansible-playbook/group_vars/all/all.yml file and save it
------------
```
---
jboss_user_password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
         xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
          xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
          xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
          xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
          xxxx
```


Compile the HelloWorld Java application
------------
```
[user@localhost ~]$ cd ansible-playbook/application/helloworld
[user@localhost helloworld]$ mvn clean package
```

**Note**
Do not forget to install maven if you want to compile the HelloWorld code.
```
[root@localhost ~]# dnf install maven
```

Execute the Ansible playbooks
------------
```
[user@localhost ~]$ ansible-playbook -i hosts --ask-vault-pass install-java-1.8.0-openjdk.yml
```

Command should ask you then you vault password

**Note**
If you did not create any SSH public and private keys
```
[user@localhost ~]$ ansible-playbook -i hosts -k --ask-vault-pass install-java-1.8.0-openjdk.yml
````

# Details
- install-java-1.8.0-openjdk -> Install OpenJDK 1.8.0
- eap-7-start -> Start Red Hat JBoss EAP 7 
- eap-7-stop -> Stop Red Hat JBoss EAP 7
- eap-7-deploy-application -> Deploy an HelloWorld application on JBoss EAP 7
- eap-7-undeploy-application -> Undeploy an HelloWorld application on JBoss EAP 7
- eap-7-enable-datasource -> enable the default ExampleDS datasource
- eap-7-disable-datasource -> disable the default ExampleDS datasource
- eap-7-create-admin -> Create an admin user in non RBAC environment
- eap-7-delete-admin -> Delete an test user in non RBAC environment
- eap-7-product-info -> Provide information on the JBoss EAP 7 Instance

# Tasks List
- [x] Install Java 1.8.0
- [x] Start and Stop Red Hat JBoss EAP 7
- [x] Deploy and undeploy Java Application
- [ ] Set and unset Datasource

#License
[EUPL-1.2](./LICENSE.md)
More information at [https://joinup.ec.europa.eu](https://joinup.ec.europa.eu/sites/default/files/custom-page/attachment/eupl_v1.2_en.pdf)

Note 
This liscence does not inculde the HelloWorld Java application which is under the Apache License, Version 2.0 (the "License")

You may obtain a copy of the License at [http://www.apache.org/licenses/LICENSE-2.0](http://www.apache.org/licenses/LICENSE-2.0)

Orignial source code can be found at [https://github.com/jboss-developer/jboss-eap-quickstarts](https://github.com/jboss-developer/jboss-eap-quickstarts)
