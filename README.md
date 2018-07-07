## Deploy Java on Ubuntu 18.04
```
sudo apt-get install openjdk-8-jdk
sudo dpkg --purge --force-depends ca-certificates-java
sudo apt-get install ca-certificates-java
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

systemctl restart jenkins

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
## CICD with Containers and Kubernetes Demo
```
# Run these commands manually to create the deployment and configure the Azure load balancer
$ kubectl create deployment hellowhale --image dazdaz/hellowhale
$ kubectl expose deployment/hellowhale --port=80 --name=hellowhalesvc --type=LoadBalancer

# Add jenkins user to /etc/group as a secondary group so that he can build docker images
$ sudo usermod -G docker jenkins

# Copy Kubernetes keys to jenkins user, so that he can run kubectl commands against our cluster
$ sudo su - jenkins
$ sudo cp ~myuser/.kube/config ~jenkins/.kube/
$ chown jenkins:jenkins ~jenkins/.kube/config
$ cp ~myuser/.kube/config /var/lib/jenkins/.kube/
$ chown jenkins:jenkins /var/lib/jenkins/.kube/

###################################################### J.E.N.K.I.N.S ##################################################
Configure System / Global Properties / Environment Variables
DOCKERHUB_PASS
mypassword

Jenkins Location / Sysadmin email
myuser@myhost.com

Configure System / Add GitHub Server / Override Hook URL (Github will ping Jenkins on this endpoint)
Check that this IP is really where your Jenkins server listens

GitHub / Settings / Integrations and Services
Select "Jenkins (GitHub plugib)" and add :
http://<Jenkins-IP>:8080/github-webhook/

Uncheck "Manage hook" if you don't have admin access or don't want to manage hooks from Jenkins.

Freestyle Project / hellowhale
GitHub Project / Project URL :
https://github.com/dazdaz/hellowhale.git

Source Code Management / Git / Repositories / Repository URL :
https://github.com/dazdaz/hellowhale.git

Enable GitHub hook trigger for GITScm polling

Execute Shell
# Jenkins Build Config
IMAGE_NAME="dazdaz/hellowhale:${BUILD_NUMBER}"
docker build . -t $IMAGE_NAME
docker login -u dazdaz -p ${DOCKER_HUB}
docker push $IMAGE_NAME

Execute Shell
# Deploy to Kubernetes
IMAGE_NAME="dazdaz/hellowhale:${BUILD_NUMBER}"
kubectl set image deployments/hellowhale hellowhale=$IMAGE_NAME

$ sudo service jenkins restart

Go back to GitHub repo / Settings Integrations / Your integration

Create a new file within the repo or update a file and then select Test Service
https://github.com/dazdaz/hellowhale/settings

$ git add html/indexl.html
$ git commit -m "Changed color to red"
$ git push

kubectl rollout history deployment/hellowhale
```
## Blue Ocean
* Blue Ocean is a new interface for Jenkins
* https://jenkins.io/projects/blueocean/
* https://jenkins.io/projects/blueocean/roadmap/ Blue Ocean is still a WIP, features are missing and the UI for me was very slow!
```
Manage Plugins / Blue Ocean
Open Blue Ocean (menu on the left)
```
# List of all Jenkins plugins
* https://updates.jenkins-ci.org/download/plugins/
* http://www.inanzzz.com/index.php/post/jnrg/running-jenkins-build-via-command-line

## Links
* https://stackoverflow.com/questions/tagged/jenkins
* https://www.digitalocean.com/community/tutorials/how-to-set-up-continuous-integration-pipelines-in-jenkins-on-ubuntu-16-04
* http://www.scmgalaxy.com/tutorials/complete-guide-to-use-jenkins-cli-command-line


## Deploying Jenkins from Docker (Fails on selecting plugins) - Don't use.
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

docker exec -it [containerid]
/usr/local/bin/install-plugins.sh [plugins]
http://jenkinsURL/safeRestart

```
## Create Jenkins Dockerfile
# Dockerfile
```
FROM jenkins/jenkins:lts
COPY plugins.txt /usr/share/jenkins/ref/plugins.txt
RUN /usr/local/bin/install-plugins-sh < /usr/share/jenkins/ref/plugins.txt
```
## Jenkins (old)
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

## Random Jenkins tips
```
http://<Jenkins-IP>:8080/restart
```
