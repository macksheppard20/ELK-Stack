# ELK-Stack
This repository contains yaml configuration files and documentation within my ELK Stack
 Automated ELK Stack Deployment
The files in this repository were used to configure the network depicted below.
![TODO: Update the path with the name of your diagram](Images/diagram_filename.png)
These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the YAML file may be used to install only certain pieces of it, such as Filebeat.

Filebeat-playbook.yml
This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
 - Beats in Use
 - Machines Being Monitored
- How to Use the Ansible Build

[File-beat.yml configuration]
```
---
- name: Installing and Launching filebeat
  hosts: webservers
  become: true
  tasks
  
  -name: download filebeat .deb
   command: curl -L -O https://artifacts.elastic.co/downlaods/beats/filebeat/filebeat-7.4.0-amd64.deb
  
  -name: install filebeat.deb
   command dpkg -i filebeat-7.4.0-amd64.deb
  
  -name: drop in filebeat.yml 
  copy: 
     src: /etc/ansible/files/filebeat-config.yml
     dest: /etc/filebeat/filebeat.yml
     
  -name: enable and configure system module
  command: filebeat modules enable system
  
  -name: setup filebeat
  command: filebeat setup
  
  -name: start filebeat service
  command: service filebeat start
```

Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.
Load balancing ensures that the application will be highly efficient in the way it distributes the data load, in addition to restricting uneven overload to nodes while other nodes are idle in the network.

What aspect of security do load balancers protect? What is the advantage of a jump box?

   - Load balancers have the ability to add additional layers of security to your network without any changes to the application 

   - Load balancers also protect applications from threats and protect the network from hackers with daily rule updates.

   -  The advantage to having a jump box is for the sole purpose of convenience and support. A jump box is an OOB remote access method so the network can be maintained constantly without having a direct data center. 

[NOTE: Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the network and system logs.]

What does Filebeat watch for? 

Filebeat watches the log files and locations which they originate from. File beat will monitor these log files and send them to Elasticsearch or Logstash for indexing.

What does Metricbeat record?
  - Similar to how Filebeat operates, Metricbeat records and collects metrics from the operating system and services actively running on the server and sends the data to Elasticsearch or Logstash for indexing. 
The configuration details of each machine may be found below.

| Name     | Function | IP Address       | Operating System |
|----------|----------|------------------|------------------|
| Jump Box | Gateway  | 52.149.181.212   | Linux           |
| Web 1    |  VM      | 10.0.0.5         | Linux	         |
| Web 2    |  VM      | 10.0.0.6         | Linux           |
| Elk-VM   |  VM      | 10.2.0.4         | Linux           |

Access Policies

The machines on the internal network are not exposed to the public Internet.
Only the Jump-Box  machine can accept connections from the Internet. 
Access to this machine is only allowed from the following IP addresses: 10.0.0.4

Which machine did you allow to access your ELK VM? What was its IP address? 
The only machine that can access the ELK-VM is the Jump-box through the ansible container where the YAML has been configured.
 
A summary of the access policies in place can be found in the table below.
| Name     | Publicly Accessible | Allowed IP Addresses      |
|----------|---------------------|---------------------------|
| Jump Box | Yes                 | 10.0.0.5 10.0.0.6 10.2.0.4|
| Web 1    | No                  |                      
| Web 2    | No                  |
| ELK-VM   | No                  | Local IP and 10.0.0.4                      




Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because…

What is the main advantage of automating configuration with Ansible?        - By automating configuration processes, this allows every task to become more efficient by having to maintain the files less often and eliminate the risk of error.
 
ELK INSTALLATION

The playbook for the ELK installation is as follows: [NOTE: These files have been tested and used for a live ELK deployment through Azure].

The playbook implements the following tasks:
Install docker.io
Install python-pip3
INstall docker module 
Download and launch a docker elk container
Enable the docker services

[Elk Installation Playbook.yml configuration]
```
---
- name: Config Web VM with Docker
  hosts: webservers
  become: true
  tasks: 
  - name: docker.io
    apt: 
      force_apt_get: yes
      update_cache: yes
      name: docker.io
      state: present
      
  - name: Install pip3
    apt: 
      force_apt_get: yes
      name: python3-pip
      state: present
      
  - name: Install Docker python module
    pip: 
      name: Docker
      state: present
      
  - name: download and launch a docker wb container 
    docker_container: 
      name: dvwa
      image: cyberxsecurity/dvwa
      state: started
      published_ports: 80:80
      
  - name: Enable docker service
    systemd: 
      name: docker
      enabled: yes
```


The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.
![TODO: Update the path with the name of your screenshot of docker ps output](Images/docker_ps_output.png)

Target Machines & Beats
```
This ELK server is configured to monitor the following machines:
We have installed the following Beats on these machines:
Web 1: 10.0.0.5
Web 2: 10.0.0.6
Web 3: 10.0.0.7
Beats successfully installed on these machines: 
-Filebeat
-MetricBeat  
```

These Beats allow us to collect the following information from each machine: 
   -Filebeat collects the log files and sends the data to Elasticsearch or Logstash for indexing
   -Metricbeat collects metrics and serves the same purpose by sending the data from the machines to Logstash or Elasticsearch for indexing. 

Using the Playbook

In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned:

SSH into the control node and follow the steps below:
   - Copy the install-elk.yml file to /etc/ansible
   - Update the hosts file to include [elkadmin]
   - 10.2.0.4 ansible_python_interpreter=/usr/bin/python3 following  the name of the machine
   - Run the playbook, and navigate to http://[public IP of the Machine] to check that the installation worked as expected.
   - You should now be at the home page of Kibana



 Answer the following questions to fill in the blanks:

Which file is the playbook?
Install-elk.yml

Where do you copy it?
/etc/ansible/ 

Which file do you update to make Ansible run the playbook on a specific machine? 
/etc/ansible/hosts

How do I specify which machine to install the ELK server on versus which to install Filebeat on?
Within the /etc/ansible/hosts file you can distinguish groups by setting ELK servers to their own group

Which URL do you navigate to in order to check that the ELK server is running?
http:// [IP of the machine]:[5601]/app/kibana/home
This will take you to the home page of Kibana
