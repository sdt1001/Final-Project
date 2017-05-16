# Final-Project


GITHUB and DOCKER


Create a github profile at https://github.com. 

In the github profile, https://github.com/sdt1001?tab=repositories, create a new repository by clicking the “New” button. 

Name the repository “Final-Project” with a read me file and clicked on the “Create repository” button. 

In command line, type “git clone” and the repository of the new git hub repository, so https://github.com/sdt1001/Final-Project.git 

Then in that command line, cd into the new directory with “cd Final-Project”.

Now in command line, “git checkout -b Project” which creates a new branch off master to work in. 

Utilize a Dockerfile in sublime to install, start and run essential services like Ubuntu, python and flask. 

Add another file via sublime named run_tests.sh to run the flask test using python and a py file named unh698_test. 

Next is a py file that will import Flask and render_template from flask as well as import setup_metrics from Prometheus_metrics. This will also def indexPage and topicPage. I’m calling this unh698.py

Then there is a test file that imports unittest and creates a class FlaskrTestCase that defines test cases for setUp, tearDown, and testing of each individual web page. 

Next is a docker-compose.test.yml file done in sublime to run the tests in run_tests.sh file when we create a build in docker. 

Utilize an Ansible Playbook to run everything automatically inside Docker. In the Playbook provided, within the deploy-website-production.yml and the deploy-website-staging.yml are lines for “*_image_version: release-….”. Set both to 0.0.1 to start. Once changes are made to be pushed later, the staging version will increase to 0.0.2. This will be based on the “git tag” that will be explained later. 

Utilized a templates folder for the web pages. 

Locally via command line, perform a “git status” to see what files have been added/updated. 

Now in command line, do a “git add .” to add all of the files and/or folders, and another “git status” to see the names changed from red to green. 

Next perform a git commit to commit the changes to the files: “git commit -m “committing dockerfiles, ansible, prometheus and templates”. 

Now perform a git push including the name of the branch you are working on: “git push origin Project” where “Project” is the name of the branch. Everything just committed is now pushed to github. 

Before dealing with the pull request on github, we will create a new repository in dockercloud https://cloud.docker.com/swarm/sdt1001/repository by clicking on the “Create” button. Name the repository as desired – I used “final-project”. 

Still in dockercloud, go to the “Builds” section of the new repository. Click on the “Link to GitHub” button if it says “Connected” at the bottom of the button. Select your source organization, then select the source repository, then in build location select the speed for the node. Under “BUILD RULES”, click on the + symbol next to that title. Then under “Source Type”, select “Tag”, then under “Source” type in “/^[0-9,]+$/”, under “Docker Tag” type in “release-{sourceref}”, under “Dockerfile location” type in “Dockerfile”. Click on the “Save” button. 

Go back to github. In the horizontal list of tabs under username/repository name, select “Settings”. Next, click on “Integrations & services” within the left side options. Next, in “Services”, click on the “Add service” drop down menu, scroll down to “Docker” and click on “Docker”. Confirm github password. Next, click on “Add service” green button at the bottom of the page. 

Still in github, within the “Final-Project” repository, a recently pushed branch appears with a button “Compare & pull request”, click that button. Then, click the “Create pull request” green button. So long as there aren’t any conflicts, click the “Merge pull request” green button, then the “Confirm merge” green button. 

This should trigger a build in dockercloud. 

Once successfully built in dockercloud, locally in command line do a “git pull origin master” so that your local repository is the same as the github master repository after the merge of the pull request was completed. 

To do a git tag as mentioned above, go to the local command line and change branches if necessary, to master by “git checkout master”. Then perform a “git pull origin master”. Now type “git tag” followed by the desired tag number, in this case 0.0.1 so “git tag 0.0.1”. Then do a “git push –tags origin master” which pushes the tag that was just created to github. This also will 
trigger a build with dockercloud in the tag that was just pushed. 


AWS INSTANCE


Now it is time to utilize the AWS instance provided via an IP address and an AWS ssh key. Utilizing the IP address of 54.215.133.212 enter the following via command line interface:
	ssh -i file path of key file location keyfile name servername@ipaddress 
	SO…
	ssh -i c:\Users\Steve\Desktop\awskey\sthibault-1492664695.key sthibault@54.215.133.212

Let’s configure the server with the following commands:
	sudo apt install docker.io
	sudo apt-get update
	(if the daemon doesn’t work….)
	sudo usermod -aG docker $(whoami)
	(logout)
	(log in)

After logging back into your AWS instance, check that a simple docker container can be run with the following:
	docker run ubuntu:xenial echo “hello world”

Pulls are completed and “hello world” shows at the end.

Git clone a copy of your docker-cloud-test repo onto the server.

	git clone your docker-cloud-test repository within the server you are ssh’d into.
	SO….
	git clone https://github.com/sdt1001/Final-Project.git

Now we can build a local image of the docker cloud Flask server with the following commands: 

	cd Final-Project 
	docker build -t test .

Also, verify that git works: 

	git log –oneline --decorate

If, for whatever reason, you need to pull the latest copy of your public image, use the following:
	
	docker pull sdt1001/Final-Project 

Now start a container using the public image:
	
	sudo docker run -d -p 8080:8080 sdt1001/final-project python3 unh698.py

	-d option runs the container in the background & prints container ID
returns “9f54f0be98f9e36b72d4cfae5fc78f6f7123b227a6d40a5a70e5330036412f2d”
OR if you have run this command previously and it generated an error that was fixed, now the Bind for 0.0.0.0:8080 will fail as the port is already allocated.

That’s ok. Do the following:

docker ps -a

Look at the containers that come up. Whichever container ID has “0.0.0.0:8080->8080/tcp” needs to be stopped. Within that container is a name assigned to that container, say “gigantic_bassi” (it will be the last entry for any given container). You must stop this container with the following:
docker stop gigantic_bassi (whatever the name of the container is) this returns the name of the container that has just been stopped

Now do another docker ps -a and note that the port for that container is now empty. 
Perform another “sudo docker run….” (like above)

In a web browser, type in http://54.215.133.212:8080 and your page should appear.

MULTIPLE CONTAINERS

When using a newer release, say 0.0.2, replace the currently running container with one pinned at the new release number with the following commands within the AWS instance:
	
	docker ps
	
	docker stop happy_darwin (where happy_darwin was the name of the instance)

Running the Ansible Playbooks with the following commands:

	ansible-playbook deploy-website-production.yml -v
	
	ansible-playbook deploy-website-staging.yml -v

Utilizing the link just above “MULTIPLE CONTAINERS”, the production instance should be available on the host’s port 8080 while the staging instance should be available on the host’s port 8081.