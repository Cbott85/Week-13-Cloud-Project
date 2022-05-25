## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.
https://github.com/Cbott85/Week-13-Cloud-Project/blob/main/Diagrams/Project-Diagram.png
These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the yml and config file may be used to install only certain pieces of it, such as Filebeat.


This document contains the following details:
- Description of the Topologu
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly functional, in addition to restricting traffic to the network.
	What aspect of security do load balancers protect? 

		Load balancers reroute traffic from one server to another in case of an attack such as a DDoS attack.  This helps to displace the traffic so systems can still function.

	What is the advantage of a jump box?

		A Jump Box Provisioner prevents the VM's from being exposed to outside traffic through public IP's by allowing monitoring and logging capabilities on one box while being able to restric IP addresses.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the network and system logs.

	What does Filebeat watch for?

		Filebeat monitors log files and then forwards the events to Elasticsearch for indexing.

	What does Metricbeat record?
		Metricbeat takes all of the metrics/statistics that are collected and sends them over to Elasticsearch.

The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name     | Function      | IP Address             | Operating System |
|----------|---------------|------------------------|------------------|
| Jump Box | Gateway       | 10.0.0.4/20.28.149.15  | Linux            |
| Web-1    | Ubuntu Server | 10.0.0.5/20.28.151.192 | Linux            |
| Web-2    | Ubuntu Server | 10.0.0.6/20.28.151.192 | Linux            |
| Elkserver| Ubuntu Server | 10.1.0.4/52.147.59.116 | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jump Box Provisioner machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:

	My public IP through TCP 5601.
		
Machines within the network can only be accessed by workstation and Jump Box Provisioner through SSH Jump-Box.

	Which machine did you allow to access your ELK VM?

		Jump Box Provisioner 

	What was its IP address?
		
		10.0.0.4 SSH port 22

A summary of the access policies in place can be found in the table below.


| Name      | Publicly Accessible | Allowed IP Addresses |
|-----------|---------------------|----------------------|
| Jump Box  | Yes                 | 20.28.149.15 SSH 22  |
| Web-1     | No                  | 10.0.0.4 SSH 22      |
| Web-2     | No                  | 10.0.0.4 SSH 22      |
| ElkServer | No                  | My Public IP TCP 5601|

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...

	What is the main advantage of automating configuration with Ansible?

		Ansible allows you to easily deploy applications though YAML playbooks.

		You can automate systems without using custom code.

The playbook implements the following tasks:

Configures the elk VM with specified machines.
# install_elk.yml
- name: Configure Elk VM with Docker
  hosts: elkservers
  remote_user: elk
  become: true
  tasks:

Intalls the docker.io
    # Use apt module
    - name: Install docker.io
      apt:
        update_cache: yes
        name: docker.io
        state: present

Intalls the python pip
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

      # Use command module
    - name: Increase virtual memory
      command: sysctl -w vm.max_map_count=262144

Allows more memory usage
      # Use sysctl module
    - name: Use more memory
      sysctl:
        name: vm.max_map_count
        value: "262144"
        state: present
        reload: yes

Download and launch the docker elk container, make ports 5601:5601, 9200:9200, and 5044:5044 availabe.
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

![TODO: Update the path with the name of your screenshot of docker ps output](Images/docker_ps_output.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
	IP addresses of the machines being monitored.
		
		Web-1: 10.0.0.5
		Web-2: 10.0.0.6

We have installed the following Beats on these machines:

	Filebeat:
		curl https://gist.githubusercontent.com/slape/5cc350109583af6cbe577bbcc0710c93/raw/eca603b72586fbe148c11f9c87bf96a63cb25760/Filebeat >> /etc/ansible/filebeat-config.yml

	Metricbeat:
		
		curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-7.6.1-amd64.deb

These Beats allow us to collect the following information from each machine:

	Filebeat collects the log files from files such as Apache and web servers.
	Metricbeat is used to monitor VM stats, filesystems, and network stats.

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

	Verify the Public IP address through https://www.whatsmyip.org.
	If IP has changed then you will need to update Security Rules to allow.

SSH into the control node and follow the steps below:

- Copy the yml file to ansible directory.
- Update the config files to includ user IP's and ports.
- Run the playbook, and navigate to Kibana with the elk server IP on your IP address to check that the installation worked as expected.

- Playbook Files:

	elk-playbook.txt
	docker-playbook.txt
	filebeat-playbook.txt
	metricbeat-playbook.txt
- Copied To:
	etc/ansible

- _To run the playbook on a specific machines:
	update - /etc/ansible/hosts
	Add IP of VM's

- To navigate to check that the ELK server is running:

	http://52.147.59.116:5601/app/kibana


