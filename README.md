## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![https://drive.google.com/file/d/17tthLPt_ldtGSPwpiyIKv7nKuVSS5NTy/view?usp=sharing](Images/diagram_filename.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the YML file may be used to install only certain pieces of it, such as Filebeat.

  - ELK Install        
  - Filebeat-playbook
  
This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting access to the network.
- _TODO: What aspect of security do load balancers protect? What is the advantage of a jump box?_

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the event logs and system metrics.
- Filebeat watches for log directories or specific log files


The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name     | Function | IP Address | Operating System |
|----------|----------|------------|------------------|
| Jump Box | Gateway  | 10.0.0.4   | Linux            |
| Web1     |webserver | 10.0.0.5   | Linux            |
| Web2     |webserver | 10.0.0.6   | Linux            |
| ELKServer|Security  | 10.1.0.4   | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the JumpBox machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- 99.189.75.83

Machines within the network can only be accessed by 5601.
- Jumpbox 10.0.0.4

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | Yes                 | 99.189.75.83         |
| LB       | Yes                 | open                 |
| Web1     | No                  | 10.0.0.5             |
| Web2     | No                  | 10.0.0.6             |
| ELK      | Yes                 | 99.189.75.83         |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- Ansible limits services running. system installations and update is streamlined and lightweight. 

The playbook implements the following tasks:
- Install Docker.io, pip3, and docker module
 # Use apt module
    - name: Install docker.io
      apt:
        update_cache: yes
        name: docker.io
        state: present

 # Use apt module
    - name: Install pip3
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present

 # Use pip module
    - name: Install Docker python module
      pip:
        name: docker
        state: present
- ...increase virtual memory 
# Use command module
    - name: Increase virtual memory
      command: sysctl -w vm.max_map_count=262144
- use sysctl module
# Use sysctl module
    - name: Use more memory
      sysctl:
        name: vm.max_map_count
        value: "262144"
        state: present
        reload: yes
- download and launch docker container for elk server
# Use docker_container module
    - name: download and launch a docker elk container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        published_ports:
          - 5601:5601
          - 9200:9200
          - 5044:5044

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

!https://drive.google.com/file/d/10izt_ljx4E5tcJQCmHi8Noc80O8ruNMN/view?usp=sharing(Images/docker_ps_output.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- Web 1 (10.0.0.5)
- Web 2 (10.0.0.6)

We have installed the following Beats on these machines:
- FileBeat

These Beats allow us to collect the following information from each machine:
- Filebeat is a log data shipper for local files. Installed as an agent on your servers, Filebeat monitors the log directories or specific log files, tails the files, and forwards them either to Elasticsearch or Logstash for indexing. An examle of such are the logs produced from the MySQL database supporting our application.

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the Configuration file to WebVM's.
- Update the /etc/ansible/hosts file to include webserver and ELK
- Run the playbook, and navigate to http://104.40.12.181:5601/app/kibana to check that the installation worked as expected.

_TODO: Answer the following questions to fill in the blanks:_
- _Which file is the playbook? Filebeat-configuration
- Where do you copy it?_ /etc/ansible/files/filbeat-config.yml to /etc/filbeat/filebeat.yml
- _Which file do you update to make Ansible run the playbook on a specific machine? /etc/ansible/hosts. How do I specify which machine to install the ELK server on versus which to install Filebeat on?_ Setting separate groups in hosts file with IPs of target machines for each desired install. 
- Update filebeat-config.yml -- specify which machine to install by updating the host files with IP addresses of web/elk servers and selecting which group to run on in ansible. 
- _Which URL do you navigate to in order to check that the ELK server is running? http://104.40.12.181:5601/app/kibana

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._
