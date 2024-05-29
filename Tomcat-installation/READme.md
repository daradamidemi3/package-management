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
vi /opt/tomcat9/webapps/manager/META-INF/context.xml

<!--
<Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />
-->

vi /opt/tomcat9/webapps/host-manager/META-INF/context.xml

<!--
  <Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />
-->

#To create username and password
vi /opt/tomcat9/conf/tomcat-users.xml

#Paste the command below the closing tomcat tag or edit and uncomment
<user username="dav" password="admin123" roles="manager-gui,admin-gui, manager-script"/>

