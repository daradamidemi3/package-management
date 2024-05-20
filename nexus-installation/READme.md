#  **<span style="color:green">Landmark Technologies.</span>**
### **<span style="color:green">Contacts: +1437 215 2483<br> WebSite : <http://mylandmarktech.com/></span>**
### **Email: mylandmarktech@gmail.com**



## Nexus Installation And Setup In AWS EC2 Redhat Instance.
##### Pre-requisite
+ AWS Acccount.
+ Create Redhat EC2 t2.medium Instance with 4GB RAM.
+ Create Security Group and open Required ports.
   + 8081 ..etc
+ Attach Security Group to EC2 Instance.
+ Install java openJDK 1.8+ for Nexus version 3.15

## Create nexus user to manage the Nexus server
```sh
#As a good security practice, Nexus is not advised to run nexus service as a root user, 
# so create a new user called nexus and grant sudo access to manage nexus services as follows. 
sudo hostname nexus
sudo useradd nexus
# Grand sudo access to nexus user
sudo echo "nexus ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/nexus
sudo su - nexus
```

### Install Java as a pre-requisit for nexus and other softwares

``` sh
cd /opt
sudo yum install wget git nano unzip -y
sudo yum install java-11-openjdk-devel java-1.8.0-openjdk-devel -y
```
### Download nexus software and extract it (unzip).
```sh
To get updated nexus version ------https://help.sonatype.com/en/download.html----
sudo wget https://download.sonatype.com/nexus/3/nexus-3.68.1-02-java11-unix.tar.gz
sudo tar -zxvf nexus-3.68.1-02-java8-unix.tar.gz
sudo mv /opt/nexus-3.68.1-02 /opt/nexus
```

## Grant permissions for nexus user to start and manage nexus service
```sh
# Change the owner and group permissions to /opt/nexus and /opt/sonatype-work directories.
sudo chown -R nexus:nexus /opt/nexus
sudo chown -R nexus:nexus /opt/sonatype-work
sudo chmod -R 775 /opt/nexus
sudo chmod -R 775 /opt/sonatype-work
```
##  Open /opt/nexus/bin/nexus.rc file and  uncomment run_as_user parameter and set as nexus user.
## # change from #run_as_user="" to [ run_as_user="nexus" ]

```sh
vi /opt/nexus/bin/nexus.rc
```

##  CONFIGURE NEXUS TO RUN AS A SERVICE 
```sh
sudo ln -s /opt/nexus/bin/nexus /etc/init.d/nexus

#9 Enable and start the nexus services
sudo systemctl enable nexus
sudo systemctl start nexus
sudo systemctl status nexus
echo "end of nexus installation"
```
# GO TO YOUR BROWSER
```sh
Type the nexus server IP:port no  eg 51.76.13.156:8081
Disable Anonymous
Copy the link to your ssh client to get the password
create new password
```
# TO CREATE REPO ON NEXUS (On the Browser)
```sh
Click on Setting icon
Click on Repositories
Create New Repository
Select (maven2 hosted)
Create for Snapshot and Release respectively
Copy the links for both Snapshot and Release Repositories to notepad
```
# TO BUILD AND SAVE TO NEXUS REPOS 
```sh
Go to maven server
mvn clean   ------ to remove the initial build (target)
Go to your maven server
cd into the maven project directory
vi into pom.xml
Go to <distribution management> tag
Remove the old urls for both Release and Snapshot
Replace the URLS with the new ones created and copied
```

# Still on Maven Server
```sh
sudo vi /opt/maven/conf/settings.xml     --------- To change the settings that will create authentication for nexus
Locate <servers> tag
Ensure that your own tag is between the last closing comment ---> and the last  </servers>

 <server>
      <id>deploymentRepo</id>
      <username>repouser</username>
      <password>repopwd</password>
 </server>
Edit <id> to nexus
Edit <username> to admin ---------- the username for your nexus
Edit <password> to admin123 -------- the password you created when login to nexus for the first time
```
# Go Back to Maven project Repo
```
ls
cd to maven project repo
mvn deploy

----Snapshot build is for DEMO
----Release build is for PRODUCTION oR DEPLOYMENT

NOTE: mvn deploy will build and save to nexus on SNAPSHOT by default

To build for RELEASE
vi pom.xml
change <version>0.0.1-SNAPSHOT</version>  to    <version>0.0.1</version>
Then build again ---- mvn deploy

NOTE: Snapshot by default allows for redeploy while Release does not
      If you have to allow redeploy on NEXUS, it only overwrite but not version.
      To redeploy in Release, change the <version> number eg <version>1.0.1</version> in pom.xml

```
# DELETING MAVEN REPOSITORY
```sh
sudo rm -rf ~/.m2/repository   ----- It deletes maven repo
cd ~/.m2/repository    ----- To check the maven repo

mvn deploy ------ It will download dependencies and plugins, build and save to nexus after this deletion

Note: While downloading dependencies from maven central, there could man-in-the-middle attack.
      This leads to setting up nexus proxy repo
``` 

# DEMILITARIZED ZONE TO MILITARIZED ZONE -- SETTING UP PROXY REPO ON NEXUS TO PREVENT MAN-IN-THE-MIDDLE ATTACKS
```sh
Maven central is a demilitarized zone
Nexus Proxy Repo is a Militarized zone

How to Set up Nexus Proxy Repo
Go to setting on Nexus
Click on Repositories
Scroll down to (maven2 proxy)
Create your project repo
Version Policy = mixed (Not Snapshot or Release)
Declare maven central url on Remote Storage (https://repo.maven.apache.org/maven2/)  where dependencies and plugins are accessed from
In this case Nexus is the one accessing the maven central and not the maven build server.
Copy the url  of the nexus proxy repo created
On maven build server:  sudo vi /opt/maven/conf/settings.xml
Go to <mirror> tag before the closing tag
 <mirror>
      <id>nexus</id>  --------------- change id to nexus 
      <mirrorOf> * </mirrorOf> ------------ the asterik means mirror everyting
      <name>Nexus Proxy Repo</name> ------------ Create a name
      <url> your nexus proxy repo link </url> ------ Put your nexus proxy repo url here and the blocked tag
    </mirror>

Go to pom.xml
vi pom.xml
Before <dependencies> tag
create <repositories> tag
<repositories>
  <repository>
     <id>nexus</id>  ----------- As provided in <mirror> in settings.xml
     <name>Nexus Proxy Repo</name>  ----------- As provided in <mirror> in settings.xml
     <url> your nexus proxy repo link </url>  ----------- As provided in <mirror> in settings.xml
  </repository>
</repositories>

Now: mvn deploy
```












