# Deploy on Docker host server using Jenkins 
# *Jenkins Job name:* `Deploy_on_Docker_Host`

### Pre-requisites

1. Jenkins server 
1. Docker-host Server 

### Integration between Docker-host and Jenkins

Install "publish Over SSH"
 - `Manage Jenkins` > `Manage Plugins` > `Available` > `Publish over SSH`

Enable connection between Docker-host and Jenkins

- `Manage Jenkins` > `Configure System` > `Publish Over SSH` > `SSH Servers` 

	- SSH Servers:
                - Name: `docker-host`
		- Hostname:`<ServerIP>`
		- username: `dockeradmin`
               
       -  `Advanced` > chose `Use password authentication, or use a different key`
		 - password: `*******`
		 
   - After above configuration go for test connection, if it fails then we need to enable PasswordAuthentication on Docker-Host.
   - For making PasswordAuthentication enable on Docker-Host VM follow below steps
   - vi /etc/ssh/sshd_config
   - search for "PasswordAuthentication" and write yes against it. Save and exit. Reload sshd service
   - service sshd reload
   - Again go back to Jenkins and test the connection, this time it will pass.
 
### Steps to create "Deploy_on_Docker_Host" Jenkin job
 #### From Jenkins home page select "New Item"
   - Enter an item name: `Deploy_on_Docker_Host`
     - Copy from: `Deploy_on_Tomcat_Server`
     
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
      - SSH Server Name: `docker-host`
       - `Transfers` >  `Transfer set`
            - Source files: `webapp/target/*.war`
	       - Remove prefix: `webapp/target`
	       - Remote directory: `//home//ansadmin` or `.`
	 

Save and run the job now.
