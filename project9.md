## TOOLING WEBSITE DEPLOYMENT AUTOMATION WITH CONTINUOUS INTEGRATION. INTRODUCTION TO JENKINS

In our project we are going to utilize Jenkins CI capabilities to make sure that every change made to the source code in GitHub `https://github.com/nicedozie4u/tooling` will be automatically be updated to the Tooling Website.

Enhance the architecture prepared in Project 8 by adding a Jenkins server, configure a job to automatically deploy source codes changes from Git to NFS server.

Here is how your updated architecture will look like upon competion of this project:

![add jenkins](./images/add_jenkins.png)

### INSTALL AND CONFIGURE JENKINS SERVER

Create an AWS EC2 server based on Ubuntu Server 20.04 LTS and name it "Jenkins"

![Jenkins](./images/Jenkins.PNG)

Install JDK (since Jenkins is a Java-based application)

```
sudo apt update
sudo apt install default-jdk-headless
```

![jdk](./images/install%20JDK.PNG)

Install Jenkins

```
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt-get install jenkins
```

![jenkins](./images/install%20Jenkins1.PNG)

Make sure Jenkins is up and running

`sudo systemctl status jenkins`

![jenkins status](./images/check%20jenkins%20status.PNG)

By default Jenkins server uses TCP port 8080 – open it by creating a new Inbound Rule in your EC2 Security Group

![open port 8080](./images/open%20port%208080.PNG)

Perform initial Jenkins setup.
From your browser access `http://<Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>:8080`

You will be prompted to provide a default admin password

![setup jenkins](./images/setup%20Jenkins.PNG)

Retrieve it from your server:

`sudo cat /var/lib/jenkins/secrets/initialAdminPassword`

![show password](./images/show%20password.PNG)

Then you will be asked which plugings to install – choose suggested plugins.

![customize jenkins](./images/customize%20jenkins.PNG)

Once plugins installation is done – create an admin user and you will get your Jenkins server address.

The installation is completed!

![jenkins](./images/Jenkins%20complete.PNG)

### Configure Jenkins to retrieve source codes from GitHub using Webhooks

In this part, you will learn how to configure a simple Jenkins job/project (these two terms can be used interchangeably). This job will will be triggered by GitHub `webhooks` and will execute a ‘build’ task to retrieve codes from GitHub and store it locally on Jenkins server.

Enable webhooks in your GitHub repository settings

![enable webhook](./images/add%20web-hook1.PNG)

![enable webhook2](./images/add%20web-hook2.PNG)

Go to Jenkins web console, click "New Item" and create a "Freestyle project"

![jenkin freestyle](./images/create%20freestyle%20project.PNG)


To connect your GitHub repository, you will need to provide its URL, you can copy from the repository itself

![copy repo](./images/connect%20git%20repo.PNG)


In configuration of your Jenkins freestyle project choose Git repository, provide there the link to your Tooling GitHub repository and credentials (user/password) so Jenkins could access files in the repository.

![jenkins config](./images/configure%20jenkins.PNG)

Save the configuration and let us try to run the build. For now we can only do it manually.
Click "Build Now" button, if you have configured everything correctly, the build will be successfull and you will see it under `#1`

![test build](./images/success1.PNG)

You can open the build and check in "Console Output" if it has run successfully.

![test build](./images/success2.PNG)

![test build](./images/success3.PNG)

If so – congratulations! You have just made your very first Jenkins build!

But this build does not produce anything and it runs only when we trigger it manually. Let us fix it.

Click "Configure" your job/project and add these two configurations
Configure triggering the job from GitHub webhook:

![config trigger](./images/config%20build%20trigger.PNG)

Configure "Post-build Actions" to archive all the files – files resulted from a build are called "artifacts".

![post build](./images/config%20post%20build%20action.PNG)

Now, go ahead and make some change in any file in your GitHub repository (e.g. README.MD file) and push the changes to the master branch.

You will see that a new build has been launched automatically (by webhook) and you can see its results – artifacts, saved on Jenkins server.

![second build](./images/build%20artifact.PNG)

![second build](./images/push%20success.PNG)

You have now configured an automated Jenkins job that receives files from GitHub by webhook trigger (this method is considered as ‘push’ because the changes are being ‘pushed’ and files transfer is initiated by GitHub). There are also other methods: trigger one job (downstreadm) from another (upstream), poll GitHub periodically and others.

By default, the artifacts are stored on Jenkins server locally

`ls /var/lib/jenkins/jobs/tooling_github/builds/<build_number>/archive/`

![check artifact](./images/check%20artifacts.PNG)

