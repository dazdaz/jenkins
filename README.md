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

# https://www.digitalocean.com/community/tutorials/how-to-set-up-continuous-integration-pipelines-in-jenkins-on-ubuntu-16-04
# http://www.scmgalaxy.com/tutorials/complete-guide-to-use-jenkins-cli-command-line

## Configure acs-engine
```
mkdir $HOME/gopath
# Add to $HOME/.profile
cat >> $HOME/.profile <<_EOF_
export PATH=$PATH:/usr/local/go/bin
export GOPATH=$HOME/gopath
_EOF_
source $HOME/.profile
go get github.com/Azure/acs-engine
go get all
cd $GOPATH/src/github.com/Azure/acs-engine
go build
./acs-engine
```

# https://github.com/Azure/acs-engine/blob/master/docs/acsengine.md
