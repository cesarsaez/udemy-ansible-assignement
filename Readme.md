# Udemy course: Ansible Advanced - Hands-On - DevOps

## Ansible assignement

As part of the Udemy course [Ansible Advanced - Hands-On - DevOps](https://www.udemy.com/course/learn-ansible-advanced/) course there is an assigment at the end to test your learned skills. Intructor uploaded its solution using GCP here https://github.com/mmumshad/udemy-ansible-assignment

In my solution, I'm going to make **use of Azure** as cloud provider running **Centos 8** VMs, therefore we had to adapt all the infra to Azure setting and some packages are different from the instructors solution, specially due Python2 deprecation.

## MacOS Catalina, Ansible and Azure

![](https://redislabs.com/wp-content/uploads/2016/11/Microsoft-Azure-logo-200x200-official.png) ![Ansible](https://upload.wikimedia.org/wikipedia/commons/0/05/Ansible_Logo.png) ![Catalina](https://i.blogs.es/2ab5f0/workfeatured-macos-catalina-icon/200_200.jpg)

This combination is not straighforward with the default values. Actually if Ansible was installed using Homebrew (brew install ansible) just remove it from Brew and install it through Pip3 otherwise you will suffer a dependency hell
```
pip3 install ansible \
    ansible\[azure\] \
    packaging \
    msrest \
    msrestazure \
    azure \
    azure.storage \
    pyOpenSSL \
    cryptography \
    certifi
```

## Requirements

Deploy a web applicaiton using the following restrictions:

- 3 VM (2 web server and 1 database)
- Install Python dependecies
- Install Flask and Mysql in a distributed model
- Install App from a Git repo (https://github.com/mmumshad/simple-webapp)
- Run the services
- Configure Load Balancer
- Send email notification once deployment is completed.

**Note:** Mysql is listening by default on 127.0.0.1  update it on /etc/mysql/my.conf. As well as allow the Mysql user to connect from source IP or % 

### High level steps for Azure

In Azure there are few prerequisites before creating VMs.

Check this article for details **how to connect Ansible with Azure** https://dev.to/joshduffney/connecting-to-azure-with-ansible-22g2

- Create App Registration and get its Service Principal IDs and store it under $HOME/.azure/credentials
- Grant Service Principal the proper rights to create vNET, LB, VMs
- Create vNet, Subnet and NSG to allow public access to HTTP to the LB
- Create/Publish on your computer an SSH key to connect to the VMs
- Create NSG rule to allow SSH from your Ansible controller public IP
- Create Public IPs
- Create NICs
- Create Availability Set
- Create a Public Load Balancer with basic setting and an empty Backend Address Pool
- Link the Public IPs to the NIC in order to connect through SSH with Ansible 
- Link the Availablity Set with the WEB server NICs
- Link the NICs with the empty Backend Address Pool
- Create VMs
- Gather facts about the Public IPs in order to map hostname and assigned public IP
- Create an in-memory (add_host module ) inventory with the hostnames, public IPs and assign it to groups filtering its hostname.
- Configure the DB server software 
- Configure the WEB server software 

To Do:

- Send notification email/slack with details of the public IPs of the servers and the Load Balancer IP
- Complete the Load Balancer configuration of the Health Probes, Inbound NAT rules, etc.
- Clean tags used for develpment of the playbooks


### Running the playbooks

Note the following assumptions:

- Variables are set at **group_vars/all.yml**
- Use **main.yml** as the only entry playbook.
- The **main.yml** has 3 blocks in order to delegate certain task to localhost, for instance the Azure infra is delegated to localhost and the DB and WEB are delegated to the in-memory inventory groups db_servers and web_servers

In order to avoid SSH asking to confirm *Are you sure you want to continue connecting (yes/no)?* set the ANSIBLE_HOST_KEY_CHECKING to False when invoking the playbook
```
export ANSIBLE_HOST_KEY_CHECKING=False \
ansible-playbook main.yml
```