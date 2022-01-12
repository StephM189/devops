# Webhook configuration with Jenkins and Gogs

## Install the Gogs plugin

- open the Manage Jenkins Page http://localhost:8080/manage
- click _Manage Plugins_ icon
- click on the _AVAILABLE_ tab
- search for _Gogs_
- select the _Gogs_ plugin for installation
- click _Install without restart_ button
- don't restart Jenkins, once the plugin is installed go back to dashboard http://localhost:8080/

## Configure your Hello-World pipeline for automated build

- click _hello-world_ job.
- click _Configure_ in the job page to edit the job.
- scroll to _Build Triggers_ section
- select the checkbox _Build when a change is pushed to Gogs_
- click on _Save_

## Configuration on the Gogs server. [Gogs Webbook](https://plugins.jenkins.io/gogs-webhook/)

- open the _devops_ repository page http://localhost:3000/thecodecamp/devops
- click on _Settings_ on the top right side of the page.
- click on _Webhooks_ on the left panel.
- select _Gogs_ in _Add a new webhook_ dropdown.
- enter _Payload URL_ http://localhost:8080/gogs-webhook/?job=hello-world
- leave rest of the settings as it is.
- click on the _Add webhook_ button.

## Testing the above configuration

- edit the file [HelloWorld.java](../jenkins/hello-world/src/HelloWorld.java)
- change the text _"Hello, World"_ to _"Hello, World automated!!"_

Run the below commands to make a commit and push to devops repository:
```
git add labs/jenkins/hello-world/src/HelloWorld.java
git commit -m 'Checking automated build'
git push gogs
```
- check the Jenkins server, the build should have started for _hello-world_ job.