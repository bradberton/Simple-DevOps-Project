# Ansible Installation

Ansible is an open-source automation platform. It is very, very simple to set up and yet powerful. Ansible can help you with configuration management, application deployment, task automation.

### Pre-requisites

1. An AWS EC2 instance (on Control node)

### Installation steps:
#### on Amazon EC2 instance

1. Install python and python-pip
   ```sh
   yum install python -y 
   yum install python-pip -y
   pip install docker
   ```
1. Install ansible using pip check for version
    ```sh
    pip install ansible
   ansible --version
   ```
   
1. Create a user called ansadmin (on Control node and Managed host)/ on ansible-server and docker-server  
   ```sh
   useradd ansadmin
   passwd ansadmin
   ```
1. Below command grant sudo access to ansadmin user. But we strongly recommended using "visudo" command if you are aware vi or nano editor.  (on Control node and Managed host)
   ```sh
   echo "ansadmin ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers
   ```
   
1. Log in as a ansadmin user on master and generate ssh key (on Control node)/ on ansible-server
   ```sh 
   sudo su - ansadmin
   ssh-keygen
   ```
1. Copy keys onto all ansible managed hosts (on Control node)/ on ansible-server
   ```sh 
   ssh-copy-id ansadmin@<local-ip-docker-server>
   ssh-copy-id ansadmin@localhost
   ```

1. Ansible server used to create images and store on docker registry. Hence install docker, start docker services and add ansadmin to the docker group. 
   ```sh
   yum install docker
   
   # start docker services 
   service docker start
   service docker start 
   
   # add user to docker group in asible server and docker server
   usermod -aG docker ansadmin
   
   #edit ssh to open connection
   /etc/ssh/sshd_config --> PasswordAuthentication yes

   ```
1. Create a directory /etc/ansible and create an inventory file called "hosts" add control node and managed hosts IP addresses to it. <-- this is done at ansible-server
 
### Validation test

   
1. Run ansible command as ansadmin user it should be successful (Master)
   ```sh 
   ansible all -m ping
   ```
