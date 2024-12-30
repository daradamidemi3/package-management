#  **<span style="color:green">Landmark Technologies, Ontario, Canada.</span>**
### **<span style="color:green">Contacts: +1437 215 2483<br> WebSite : <http://mylandmarktech.com/></span>**
### **Email: mylandmarktech@gmail.com**



## Apache Tomcat Installation And Setup In AWS EC2 Redhat Instnace.
##### Prerequisite
+ AWS Acccount.
+ Create Redhat EC2 T2.micro Instance DIFFERENT from the server used for build (MAVEN).
+ Create Security Group and open Tomcat ports or Required ports.
   + 8080 ..etc
+ Attach Security Group to EC2 Instance.
+ Install java openJDK 1.8+

### Install Java JDK 1.8+ & Tomcat version 9.0.55

``` sh
# install Java JDK 1.8+ as a pre-requisit for tomcat to run.
cd /opt 
sudo yum install git unzip tree wget -y
sudo yum install java-1.8.0-openjdk-devel -y
# Download tomcat software and extract it. For update " https://tomcat.apache.org/download-90.cgi " Right click on tar.gz and copy the link
sudo wget https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.89/bin/apache-tomcat-9.0.89.tar.gz
sudo tar -xvf apache-tomcat-9.0.89.tar.gz
sudo rm apache-tomcat-9.0.89.tar.gz
sudo mv apache-tomcat-9.0.89 tomcat9
sudo chmod 777 -R /opt/tomcat9
sudo sh /opt/tomcat9/bin/startup.sh
# create a soft link to start and stop tomcat
sudo ln -s /opt/tomcat9/bin/startup.sh /usr/bin/starttomcat
sudo ln -s /opt/tomcat9/bin/shutdown.sh /usr/bin/stoptomcat
sudo starttomcat
```
Access tomcat with your serverIP:port number on your browser  e.g 23.43.153.65:8080
#To solve the error 403 on Manager and Host
```
vi /opt/tomcat9/webapps/manager/META-INF/context.xml

<!--
<Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />
-->
```
```
vi /opt/tomcat9/webapps/host-manager/META-INF/context.xml
```
<!--
  <Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />
-->

#To create username and password
vi /opt/tomcat9/conf/tomcat-users.xml

#Paste the command below the closing tomcat tag or edit and uncomment
<user username="admin" password="admin123" roles="manager-gui,admin-gui, manager-script"/>

#HOW TO DEPLOY ON TOMCAT SERVER FROM MAVEN

1. spin up different servers for both maven and tomcat 
   Note: Ensure you have a build on maven & if not (mvn package) to build

2. On maven server (inside the maven repo), create a file with the exact name of the security key used to create the tomcat server (eg tomcat.pem),
  open the tomcat security key in notepad, copy the content and paste in the file created in maven repo to LINK the two servers together.

3. On maven server, use this command
  scp -i <name of the created file in 2> target/maven-web-app.war ec2-user@57.13.45.37:/opt/tomcat9/webapps

NOTE: syntax
scp -i <name of the created file in 2> target/maven-<war file> ec2-user@<tomcat server IP>:/opt/tomcat9/webapps

4. Change privilege of the tomcat.pem in 2 to 400:
   chmod 400

5. Use the command on 3 again after changing the privilege

6. Launch your browser, TYPE tomcat server IP:/maven-web-app

7. To hot fix bug--- this is correcting of errors by DevOps in the deployment without waiting for the dev team.
  a. Go to maven server
  b. src -> main -> webapp -> jsps
  c. vi home.jsp        to edit or fix the bug

8. Use the command below to remove old build, and rebuild new artefacts
   mvn clean package

9. To re-deploy, use the command on 3 again



