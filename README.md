## Deploy Java
```
sudo apt-get install openjdk-8-jdk
sudo dpkg --purge --force-depends ca-certificates-java
sudo apt-get install ca-certificates-java
systemctl restart jenkins
```
## Deploy Jenkins
```
sudo apt-get install jenkins
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
8888888844ce44e3bd49737688888888
# sudo ufw allow 8080
# sudo ufw enable
curl ifconfig.co
8.8.8.8
az vm open-port --port 8080 --resource-group u1804-rg --name u1804
open 8.8.8.8:8080
```
## Creating Jobs
```
New Project / Project Name : SampleBuildJob1 / Freestyle
Build Triggers / Build / Execute Shell
date
echo "Job built successfully."

New Project / Project Name : SampleDeployJob / Freestyle
Build Triggers / Build / Execute Shell
date
echo "Job built successfully."
Build after other projects are built / SampleBuildJob

New Project / Project Name : SampleTestJob / Freestyle
Build Triggers / Build / Execute Shell
date
echo "Job built successfully."
Build after other projects are built / SampleDeployJob
```

## Plugin : Delivery Pipeline
```
https://plugins.jenkins.io/delivery-pipeline-plugin
Manage Jenkins / Manage Plugins / Available / delivery

Download delivery-pipeline-plugin manually and upload the hpi file.  Workaround to plugin install hanging
https://updates.jenkins-ci.org/download/plugins/delivery-pipeline-plugin/

Goto + on Main Page
Goto Delivery Pipeline View
Pipelines / Component / Add
- Name / BuildJob
- Initial Job / SampleBuildJob

Edit View /
Enable start of new pipeline build
Enable Trigger Rebuild - run a new job within the pipeline, but don't re-run the entire pipeline
Enable Show Total Build Time
Theme / Contrast
```
## Plugin : Build Pipeline
```
Manage Plugins / Build Pipeline
https://updates.jenkins-ci.org/download/plugins/build-pipeline-plugin/
View Name : BuildPipelineTest
Enable Build Pipeline View
Select Initial Job / SampleBuildJob
No of displayed Builds / 5
```

List of all Jenkins plugins
https://updates.jenkins-ci.org/download/plugins/
http://www.inanzzz.com/index.php/post/jnrg/running-jenkins-build-via-command-line


## Deploying Jenkins from Docker (Fails on selecting plugins)
```
docker run -u root --rm -d -p 8080:8080 -p 50000:50000 -v jenkins-data:/var/jenkins_home -v /var/run/docker.sock:/var/run/docker.sock jenkinsci/blueocean
docker ps
PID=$(docker inspect --format {{.State.Pid}} 7a9b16e9b18f)
sudo nsenter --target $PID --mount --uts --ipc --net --pid
cat /var/jenkins_home/secrets/initialAdminPassword
8888888844ce44e3bd49737688888888
curl ifconfig.co
20.184.25.239
az vm open-port --port 8080 --resource-group u1804-rg --name u1804
open 8.8.8.8:8080
```

## Jenkins
```
## Deploy Jenkins
sudo wget -q -O - https://pkg.jenkins.io/debian/jenkins-ci.org.key | sudo apt-key add -
sudo echo deb http://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list
sudo apt-get update
sudo apt-get install jenkins
sudo usermod -aG docker jenkins
systemctl start jenkins
sudo systemctl status jenkins
sudo ufw allow 8080
sudo ufw enable

wget http://127.0.0.1:8080/jnlpJars/jenkins-cli.jar
JENKINSADMINPASS=$(cat /var/lib/jenkins/secrets/initialAdminPassword)
java -jar jenkins-cli.jar -s http://127.0.0.1:8080 who-am-i --username admin --password $JENKINSADMINPASS
```

* https://www.digitalocean.com/community/tutorials/how-to-set-up-continuous-integration-pipelines-in-jenkins-on-ubuntu-16-04
* http://www.scmgalaxy.com/tutorials/complete-guide-to-use-jenkins-cli-command-line

