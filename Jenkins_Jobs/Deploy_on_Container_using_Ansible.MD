# Deploy on a docker container using Ansible
# *Jenkins Job name:* `first_job_Container_on_ansible_server`

### Pre-requisites

1. Jenkins server 
1. Docker-host server 
1. Ansible server
1. create directory `/opt/docker` then `chown -R ansadmin:ansadmin /opt/docker` then create Dockerfile in docker-server and ansible-server
1. create file `Dockerfile` under *`/opt/docker`*  on Ansible server and dockcer-server **[Get Help Here]()** 
   ```sh 
   # Pull tomcat latest image from dockerhub 
   From tomcat
   # Maintainer
   MAINTAINER "AR Shankar" 

   # copy war file on to container 
   COPY ./webapp.war /usr/local/tomcat/webapps
1. Create Playbook `create-image.yml` under *`/opt/docker`* on Ansible server **[Get Help Here]()**
   ```sh
   ---
   - hosts: all
     #ansadmin doesn't need root access to create an image
     become: true 

     tasks:
     - name: building docker image
       command: "docker build -t simple-devops-image ." 
       args:
         chdir: /opt/docker
   ```
1. Create Playbook `create-container.yml` under *`/opt/docker`* on Ansible server **[Get Help Here]()**
   ```ssh
   ---
   - hosts: all
     become: ture

     tasks:
     - name: creating docker image using docker command
       command: docker run -d --name simple-devops-container -p 8080:8080 simple-devops-image
   ```

### Integration between Ansible-control-node and Jenkins

Install "publish Over SSH"
 - `Manage Jenkins` > `Manage Plugins` > `Available` > `Publish over SSH`

Enable connection between Ansible-control-node and Jenkins

- `Manage Jenkins` > `Configure System` > `Publish Over SSH` > `SSH Servers` 

	- SSH Servers:
                - Name: `ansible-server`
		- Hostname:`<ServerIP>`
		- username: `ansadmin`
               
       -  `Advanced` > chose `Use password authentication, or use a different key`
		 - password: `*******`
 
### Steps to create "first_job_Container_on_ansible_server" Jenkin job
#### From Jenkins home page select "New Item"
   - Enter an item name: `first_job_Container_on_ansible_server`
     - Copy from: `Deploy_on_Container`
     
   - *Source Code Management:*
      - Repository: `https://github.com/yankils/hello-world.git`
      - Branches to build : `*/master`  
   - *Poll SCM* :      - `* * * *`

   - *Build:*
     - Root POM:`pom.xml`
     - Goals and options: `clean install package`

 - *Post-build Actions*
   - Send build artifacts over SSH
     - *SSH Publishers*
      - SSH Server Name: `ansible-server`
       - `Transfers` >  `Transfer set`
            - Source files: `webapp/target/*.war`
	       - Remove prefix: `webapp/target`
	       - Remote directory: `//opt//docker`
	       - Exec command: 
                ```sh 
                ansible-playbook -i /etc/ansible/hosts /opt/docker/create-image.yml;
                ```

Save and run the job now.
