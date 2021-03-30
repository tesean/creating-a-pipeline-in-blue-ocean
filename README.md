# creating-a-pipeline-in-blue-ocean
This repository has been adapted from
[Create a Pipeline in Blue Ocean](https://jenkins.io/doc/tutorials/create-a-pipeline-in-blue-ocean/)

This assumes you already have docker installed and configured. 
## Launch Jenkins
Powershell:
1. `docker run --name jenkins-docker --rm --detach --privileged --network jenkins --network-alias docker --env DOCKER_TLS_CERTDIR=/certs --volume jenkins-docker-certs:/certs/client --volume jenkins-data:/var/jenkins_home --publish 3000:3000 docker:dind`
2. `docker run --name jenkins-blueocean --rm --detach --network jenkins --env DOCKER_HOST=tcp://docker:2376 --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 --volume jenkins-data:/var/jenkins_home --volume jenkins-docker-certs:/certs/client:ro --publish 8080:8080 --publish 50000:50000 jenkinsci/blueocean`
3. Open Chrome and visit `http://localhost:8080`
4. Login U: itstudent P: It$tudent5

## Create Your Pipeline
1. 


This repository is for the
[Create a Pipeline in Blue Ocean](https://jenkins.io/doc/tutorials/create-a-pipeline-in-blue-ocean/)
tutorial in the [Jenkins User Documentation](https://jenkins.io/doc/).

This tutorial uses the same application that the [Build a Node.js and React app
with
npm](https://jenkins.io/doc/tutorials/build-a-node-js-and-react-app-with-npm/)
tutorial is based on. Therefore, you'll be building the same application
although this time, completely through Blue Ocean.

The `jenkins` directory contains an example of the `Jenkinsfile` (i.e. Pipeline)
that Blue Ocean will create for you during the tutorial and the `scripts`
subdirectory contains shell scripts with commands that are executed when Jenkins
processes the "Test" and "Deliver" stages of your Pipeline.
