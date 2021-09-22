# jenkins-checks-api-setup
The goal of this repo is to explain and demonstrate 
* How to setup a jenkins server so it can use the GitHub Checks API
* Setup the needed GitHub App
* Publishing Check results to GitHub via Jenkinsfile

## Setup Jenkins server
If you don't have a Jenkins server available and want to spin up a Jenkins instance on your local machine you find instructions here.

### Prerequisites
* It is assumed you work on a Linux machine
* Docker is installed
* docker-compose is installed

### Create GitHub Token
You need a user with admin access to all repositories the checks shall be published to in order to have hooks created. \
For this user, create a token at <github_url>/settings/tokens

For the needed rights, we assume "repo", "admin:repo_hook" and "admin:org_hook" are needed.

### Start Jenkins
Once you need to make sure to create a persistency directory for Jenkins and make it writable for Docker to use it afterwards:
```
mkdir jenkins_home
sudo chmod -R 777 jenkins_home
```

Then you can start the Jenkins server:
```
docker-compose up
```

### Jenkins configuration
* Open `http://localhost:8080/` and follow the initial setup instructions, setting up an admin user and installing the community suggested plugins.
If the plugins do not install ok, you might need to check the Jenkins version in the `docker-compose.yaml` file and update to the latest available lts version (https://www.jenkins.io/download/ --> Docker).
* Additionally install these plugins (`Manage Jenkins` --> `Manage Plugins` --> `Available`)
  * Cobertura
  * Code Coverage API
  * Git Forensics
  * GitHub Checks
  * Warnings Next Generation
* Add the GitHub Enterprise server at `Manage Jenkins` --> `Configure System` --> `GitHub` --> `Add GitHub Server`
  * Name: <github_url>
  * API URL: <github_url>/api/v3
  * Credentials: Use the `Add` button to add a credential of type "Secret text" and paste in the GitHub token you created earlier. Then select that credential here. (Check correctness with `Test Connection`)
* Make the GitHub Enterprise server known at `Manage Jenkins` --> `Configure System` --> `GitHub Enterprise Servers` --> `Add`
  * API endpoint: <github_url>/api/v3
  * Name: <github_server_name>

## Forward URL with Smee.io
At https://smee.io/ --> "Start a new channel" you can get an https URL which forwards webhooks to the Jenkins server on your local machine. \
Follow the instructions there, but remember that our Jenkins runs on port 8080, so an example smee start command would be:
```
smee -u https://smee.io/<your_specific_channel> -p 8080
```

## Setup GitHub App
For the Jenkins Checks API plugin to be able to publish the necessary checks a GitHub App needs to be created. \
Therefore navigate to organization's or personal Settings page for GitHub Apps (e.g. <github_url>/settings/apps) --> `New GitHub App`

Follow the instructions from https://github.com/jenkinsci/github-branch-source-plugin/blob/master/docs/github-app.adoc to 
* create the GitHub App
  * but make sure to additionally give the repository permission for `Checks`: `Read & write`
  * and the webhook URL relates to your smee url: `https://<smee-channel-url>/github-webhook/`
* generate and convert the private key
* install the GitHub App in the organizations of your choice
* add the Jenkins credential
* and configure the GitHub Organization folder on Jenkins

## Publish check results to GitHub
Take the Jenkinsfile from this repository as an example on how to publish Checks from Jenkins to GitHub

## Some related references
- https://github.com/jenkinsci/github-branch-source-plugin/blob/master/docs/github-app.adoc
- https://www.jenkins.io/doc/pipeline/steps/checks-api/
- https://smee.io/
