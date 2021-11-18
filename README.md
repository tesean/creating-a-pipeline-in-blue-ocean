# creating-a-pipeline-in-blue-ocean
This repository has been adapted from
[Create a Pipeline in Blue Ocean](https://jenkins.io/doc/tutorials/create-a-pipeline-in-blue-ocean/)

This assumes you already have docker installed and configured. 
## Launch Jenkins
Powershell:
1. `docker network create jenkins` (first time only)
2. `docker run --name jenkins-docker --rm --detach --privileged --network jenkins --network-alias docker --env DOCKER_TLS_CERTDIR=/certs --volume jenkins-docker-certs:/certs/client --volume jenkins-data:/var/jenkins_home --publish 3000:3000 docker:dind`
3. `docker run --name jenkins-blueocean --rm --detach --network jenkins --env DOCKER_HOST=tcp://docker:2376 --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 --volume jenkins-data:/var/jenkins_home --volume jenkins-docker-certs:/certs/client:ro --publish 8080:8080 --publish 50000:50000 jenkinsci/blueocean`
4. Open Chrome and visit `http://localhost:8080`
5. Find your password: Launch Docker Desktop -> Containers\Apps -> Jenkins-BlueOcean - Logs ![Default Password](https://github.com/NsccTruroIT/creating-a-pipeline-in-blue-ocean/blob/master/jenkins/screenshots/pwSnip.png "Default Password")
6. Install Suggested Plugins && NodeJS Plugin
7. Create first user: U:itstudent P:It$tudent5
8. We should now be up and running. Test login.
9. We need to add a couple more plugins though...
10. From the Jenkins Dashboard click Manage Jenkins -> Manage Plugins -> available -> type docker
![Plugins](https://github.com/NsccTruroIT/creating-a-pipeline-in-blue-ocean/blob/master/jenkins/screenshots/plugins.png "Plugins")
11. Check off  Docker and Docker Pipelne and install.

## Create your Repo
You may choose to start with a blank repo and copy source files, you may clone the provided repo or you can create a fork of the repo:
https://github.com/NsccTruroIT/creating-a-pipeline-in-blue-ocean/

There are a few scripts in the jenkins/scripts folder that will need to be executable. If you run into errors executing these scripts you can solve it by running:
`git update-index --chmod=+x` & commiting the change `git commit -m "Executable!"`

## Create Your Pipeline
1. Go back to Jenkins and ensure you have accessed the Blue Ocean interface. To do this, make sure you:

* have browsed to http://localhost:8080/blue and are logged in
or

* have browsed to http://localhost:8080/, are logged in and have clicked Open Blue Ocean on the left.

2. In the **Welcome to Jenkins** box at the center of the Blue Ocean interface, click Create a new Pipeline to begin the Pipeline creation wizard.
Note: If you don’t see this box, click **New Pipeline** at the top right.

3. In **Where do you store your code?**, click GitHub.

4. If you have cloned or forked the provided repo follow the link to create an auth token, accept all the defaults. Paste the token in to the box to link your account to Jenkins.

![Git Repo](https://github.com/NsccTruroIT/creating-a-pipeline-in-blue-ocean/blob/master/jenkins/screenshots/git.png "Git Settings")

5. Select your repo.

6. (OPTIONAL) - Set Pipeline name. If you already have a pipeline using the same repo you'll be able to provide a different name.

7. Click **Create Pipeline**.

Blue Ocean detects that there is no Jenkinsfile at the root level of the repository’s `master` branch and proceed to help you create one. (Therefore, you’ll need to click another Create Pipeline at the end of the page to proceed.)
Note: Under the hood, a Pipeline project created through Blue Ocean is actually "multibranch Pipeline". Therefore, Jenkins looks for the presence of at least one Jenkinsfile in any branch of your repository

## Configure Your Pipeline
1. Following on from creating your Pipeline project (above), in the Pipeline editor, select docker from the Agent dropdown in the Pipeline Settings panel on the right.
![Pipeline Settings](https://github.com/NsccTruroIT/creating-a-pipeline-in-blue-ocean/blob/master/jenkins/screenshots/PipelineSettings.png "Pipline Settings")

2. In the Image and Args fields that appear, specify `node:15-alpine` and `-p 3000:3000` respectively. (Note: this requires the docker plugin, to spin up the new container for your app. Once launched you can hit in in your browser using the specifiec port: http://localhost:3000))
![Agent Section](https://github.com/NsccTruroIT/creating-a-pipeline-in-blue-ocean/blob/master/jenkins/screenshots/12-agent-section.png "Agent Section")

3. Back in the main Pipeline editor, click the **+** icon, which opens the new stage panel on the right.

4. In this panel, type `Build` in the **Name your stage** field and then click the **Add Step** button below, which opens the **Choose step** type panel.

5. In this panel, click **Shell Script** near the top of the list (to choose that step type), which opens the **Build / Shell Script** panel, where you can enter this step’s values.

6. In the **Build / Shell Script** panel, specify `npm install`.

![Shell Step](https://github.com/NsccTruroIT/creating-a-pipeline-in-blue-ocean/blob/master/jenkins/screenshots/16-specifying-a-shell-step-value.png "Shell Step")

7. ( Optional ) Click the top-left back arrow icon Return from step icon to return to the main Pipeline editor.

8. Click the **Save** button at the top right to begin saving your new Pipeline with its "Build" stage.

9. In the **Save Pipeline** dialog box, specify the commit message in the Description field (e.g. `Add initial Pipeline (Jenkinsfile)`).

![Save Pipeline](https://github.com/NsccTruroIT/creating-a-pipeline-in-blue-ocean/blob/master/jenkins/screenshots/18-save-pipeline-dialog-box.png "Pipeline Dialogue")

10. Leaving all other options as is, **click Save & run** and Jenkins proceeds to build your Pipeline.

11. When the main Blue Ocean interface appears, **click the row** to see Jenkins build your Pipeline project.
Note: You may need to wait several minutes for this first run to complete. During this time, Jenkins does the following:

* Commits your Pipeline as a Jenkinsfile to the only branch (i.e. master) of your repository.

* Initially queues the project to be built on the agent.

* Downloads the Node Docker image and runs it in a container on Docker.

* Executes the Build stage (defined in the Jenkinsfile) on the Node container.

* It's normal to see several warnings.

 (During this time, npm downloads many dependencies necessary to run your Node.js and React application, which will ultimately be stored in the local node_modules directory within the Jenkins home directory).

![Run Build](https://github.com/NsccTruroIT/creating-a-pipeline-in-blue-ocean/blob/master/jenkins/screenshots/19-initial-pipeline-downloading-npm-dependencies.png "Run Build")

## Add a Test Stage to your Pipeline

1. From the main Blue Ocean interface, click **Branches** at the top-right to access your respository’s branches page, where you can access the `master` branch.

2. Click the master branch’s "Edit Pipeline" (pencil) icon on branch to open the Pipeline editor for this branch.

3. In the main Pipeline editor, click the + icon to the right of the Build stage you created above to open the new stage panel on the right.

![Add Test Stage](https://github.com/NsccTruroIT/creating-a-pipeline-in-blue-ocean/blob/master/jenkins/screenshots/22-add-test-stage.png "Add Stage")

4. In this panel, type `Test` in the **Name your stage** field and then click the Add Step button below to open the Choose step type panel.

5. In this panel, click **Shell Script** near the top of the list.

6. In the resulting **Test / Shell Script panel**, specify `./jenkins/scripts/test.sh` and then click the top-left back arrow icon Return from step icon to return to the Pipeline stage editor.

7. At the lower-right of the panel, click **Settings** to reveal this section of the panel.

8. Click the + icon at the right of the **Environment** heading (for which you’ll configure an environment directive).

9. In the **Name** and **Value** fields that appear, specify `CI` and `true`, respectively.
![Environement](https://github.com/NsccTruroIT/creating-a-pipeline-in-blue-ocean/blob/master/jenkins/screenshots/23-environment-directive.png "Set Environment")

10. ( Optional ) Click the top-left back arrow icon Return from step icon to return to the main Pipeline editor.

11. Click the **Save** button at the top right to begin saving your Pipeline with with its new "Test" stage.

12. In the **Save Pipeline** dialog box, specify the commit message in the **Description** field (e.g. `Add 'Test' stage`).

13. Leaving all other options as is, click **Save & run** and Jenkins proceeds to build your amended Pipeline.

14. When the main Blue Ocean interface appears, click the top row to see Jenkins build your Pipeline project.
Note: You’ll notice from this run that Jenkins no longer needs to download the Node Docker image. Instead, Jenkins only needs to run a new container from the Node image downloaded previously. Therefore, running your Pipeline this subsequent time should be much faster.
If your amended Pipeline ran successfully, here’s what the Blue Ocean interface should look like. Notice the additional "Test" stage. You can click on the previous "Build" stage circle to access the output from that stage.

![Test Success](https://github.com/NsccTruroIT/creating-a-pipeline-in-blue-ocean/blob/master/jenkins/screenshots/24-test-stage-runs-successfully-with-output.png "Test Success")

## Add a final deliver stage to your Pipeline

1. From the main Blue Ocean interface, click **Branches** at the top-right to access your respository’s master branch.

2. Click the master branch’s "Edit Pipeline" icon Edit Pipeline on branch to open the Pipeline editor for this branch.

3. In the main Pipeline editor, click the + icon to the right of the **Test** stage you created above to open the new stage panel.

![Add Deliver Stage](https://github.com/NsccTruroIT/creating-a-pipeline-in-blue-ocean/blob/master/jenkins/screenshots/31-add-deliver-stage.png "Deliver Stage")

4. In this panel, type `Deliver` in the **Name your stage** field and then click the **Add Step** button below to open the Choose step type panel.

5. In this panel, click **Shell Script** near the top of the list.

6. In the resulting **Deliver / Shell Script** panel, specify `./jenkins/scripts/deliver.sh` and then click the top-left back arrow icon Return from step icon to return to the Pipeline stage editor.

![Add Next Step](https://github.com/NsccTruroIT/creating-a-pipeline-in-blue-ocean/blob/master/jenkins/screenshots/32-add-next-step.png "Add Next Step")

7. Click the **Add Step** button again.

8. In the **Choose step type** panel, type `input` into the Find steps by name field.
![Search Input](https://github.com/NsccTruroIT/creating-a-pipeline-in-blue-ocean/blob/master/jenkins/screenshots/33-choosing-input-step-type.png "Search Input")

9. Click the filtered **Wait for interactive input** step type.

10. In the resulting **Deliver / Wait for interactive input** panel, specify `Finished using the web site? (Click "Proceed" to continue)` in the **Message** field and then click the top-left back arrow icon Return from step icon to return to the Pipeline stage editor.
![Input Step](https://github.com/NsccTruroIT/creating-a-pipeline-in-blue-ocean/blob/master/jenkins/screenshots/34-specifying-input-step-message-value.png "Input Step")

11. Click the Add Step button (last time).

12. Click Shell Script near the top of the list.

13. In the resulting **Deliver / Shell Script** panel, specify `./jenkins/scripts/kill.sh`.
Note: For an explanation of this step, refer to the kill.sh file itself located in the jenkins/scripts of your forked repository on GitHub.

14. ( Optional ) Click the top-left back arrow icon Return from step icon to return to the main Pipeline editor.

15. Click the **Save** button at the top right to begin saving your Pipeline with with its new "Deliver" stage.

16. In the **Save Pipeline** dialog box, specify the commit message in the **Description** field (e.g. `Add 'Deliver' stage`).

17. Leaving all other options as is, click **Save & run** and Jenkins proceeds to build your amended Pipeline.

18. When the main Blue Ocean interface appears, click the top row to see Jenkins build your Pipeline project.
If your amended Pipeline ran successfully, here’s what the Blue Ocean interface should look like. Notice the additional "Deliver" stage. Click on the previous "Test" and "Build" stage circles to access the outputs from those stages.
![Input Pause](https://github.com/NsccTruroIT/creating-a-pipeline-in-blue-ocean/blob/master/jenkins/screenshots/35-deliver-stage-pauses-for-user-input.png "Pause")

19. Ensure you are viewing the "Deliver" stage (click it if necessary), then click the green `./jenkins/scripts/deliver.sh` step to expand its content and scroll down until you see the `http://localhost:3000` link.

20. Click the http://localhost:3000 link to view your Node.js and React application running (in development mode) in a new web browser tab. You should see a page/site with the title Welcome to React on it.

21. When you are finished viewing the page/site, click the Proceed button to complete the Pipeline’s execution.

22. Click the X at the top-right to return to the main Blue Ocean interface, which lists your previous Pipeline runs in reverse chronological order.

## Follow up (Optional)
If you check the contents of the Jenkinsfile that Blue Ocean created at the root of your forked creating-a-pipeline-in-blue-ocean repository, notice the location of the environment directive. This directive’s location within the "Test" stage means that the environment variable CI (with its value of true) is only available within the scope of this "Test" stage.

You can set this directive in Blue Ocean so that its environment variable is available globally throughout Pipeline (as is the case in the Build a Node.js and React app with npm tutorial). To do this:

1. From the main Blue Ocean interface, click Branches at the top-right to access your respository’s master branch.

2. Click the master branch’s "Edit Pipeline" icon Edit Pipeline on branch to open the Pipeline editor for this branch.

3. In the main Pipeline editor, click the Test stage you created above to begin editing it.

4. In the stage panel on the right, click Settings to reveal this section of the panel.

5. Click the minus (-) icon at the right of the CI environment directive (you created earlier) to delete it.

6. Click the top-left back arrow icon Return from step icon to return to the main Pipeline editor.

7. In the Pipeline Settings panel, click the + icon at the right of the Environment heading (for which you’ll configure a global environment directive).

8. In the Name and Value fields that appear, specify CI and true, respectively.

9. Click the Save button at the top right to begin saving your Pipeline with with its relocated environment directive.

10. In the Save Pipeline dialog box, specify the commit message in the Description field (e.g. Make environment directive global).

11. Leaving all other options as is, click Save & run and Jenkins proceeds to build your amended Pipeline.

12. When the main Blue Ocean interface appears, click the top row to see Jenkins build your Pipeline project.
You should see the same build process you saw when you completed adding the final deliver stage (above). However, when you inspect the Jenkinsfile again, you’ll notice that the environment directive is now a sibling of the agent section.

## Wrapping up
Well done! You’ve just used the Blue Ocean feature of Jenkins to build a simple Node.js and React application with npm!

The "Build", "Test" and "Deliver" stages you created above are the basis for building other applications in Jenkins with any technology stack, including more complex applications and ones that combine multiple technology stacks together.

Because Jenkins is extremely extensible, it can be modified and configured to handle practically any aspect of build orchestration and automation.

To learn more about what Jenkins can do, check out:

The Tutorials overview page for other introductory tutorials.

The User Handbook for more detailed information about using Jenkins, such as Pipelines (in particular Pipeline syntax) and the Blue Ocean interface.

The Jenkins blog for the latest events, other tutorials and updates.

In the Name and Value fields that appear, specify CI and true, respectively.
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
