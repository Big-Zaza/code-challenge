# Solution of Code Challenge

This repo contains the source code to be able to provision and deploy an Infrastructure with a CI/CD pipeline deployed.

## Instructions

Fork, then download or clone the repo.
```bash
git clone https://github.com/Big-Zaza/code-challenge.git
```

Our environment will be provisioned with terraform so as to automate the creation and removal of resources

Move to the directory that contains the terraform code
```bash
cd terraform_vpc
```
Make sure that your awscli is configured

Naviagate to the project directory where your terraform code is found

```bash
cd terraform_vpc
```

Run terraform init to intialise terraform to be able to downlaod provider plugins and prepare the working directory.

```bash
terraform init
```
Run terraform plan to preview changes and see the execution plan without making any actual changes.

```bash
terraform plan
```
Install the dependencies via the terminal.
```bash
terraform apply
```
Run terraform apply to apply the changes specified in your configuration.
```bash
terraform apply
```

And the output should be this result 

<img width="1440" alt="Screen Shot 2023-12-07 at 07 20 30" src="https://github.com/Big-Zaza/code-challenge/assets/92643385/7b782e75-93ae-4428-b5d4-8b75f6c973d6">

<b>The resources provisioned are a VPC with 2 public and private subnets. </b?
<b>In each public subnet, we have an ec2 instance that is public_subnet_a and public_subnet_b. </b>
<b>The Vpc has an internet gateway attached to it, to enable the public subnet to access the internet</b>
<b>Traffic is distributed to the 2 ec2 intances because of an Application Load balancer</b>
<b>The ec2 instances are automatically scaled because they're attached to an autoscaling group </b>

##Nodejs application deployment with a Jenkins pipeline

The deployment of our Nodejs application to our 2 ec2 instances is done using a Jenkins CI/CD pipeline because it allows us to automate the entire deployment process, from source code retrieval to application deployment with little or no manual intervention. 

Jenkins was installed on one of our ec2 instances using this script.

```bash
#!/bin/bash
# Install java as jenkins dependency
sudo apt install openjdk-11-jdk -y
# install jenkins in ubuntu:
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'
sudo apt-get update
sudo apt-get install jenkins -y
sudo systemctl start jenkins 
sudo usermod -aG docker jenkins 
echo "jenkins  ALL=(ALL) NOPASSWD:ALL" | sudo tee -a /etc/sudoers.d/jenkins 
docker --version
sudo su - jenkins
```

Once Jenkins is installed, you can access it from the User Interface using your [http://publicip:8080](http://publicip:8080) 

From there you can provide your create an account and login.

<img width="1389" alt="Screen Shot 2023-12-07 at 07 40 00" src="https://github.com/Big-Zaza/code-challenge/assets/92643385/fee3090d-06c9-4352-afbb-dbf735d36c9a">


Once in the Jenkins platform, you move to Manage Jenkins and then install this important plugins that will help us in the deployment of our Nodejs application; <b> Nodejs, Docker, SonarQube, Slack Notification </b>

Once it's done, we'll move to new item and create a pipeline.

Give your pipeline a name

Go to the section of pipeline and select Pipeline from SCM (Source Code Manager) where we'll reference this git repository that is hosting our application source code and the Jenkins file that will be used to build and deploy the application source code.


<img width="999" alt="Screen Shot 2023-12-07 at 07 47 22" src="https://github.com/Big-Zaza/code-challenge/assets/92643385/64b3eb4a-dc4e-4d7c-9c50-ce3216a6783c">

Then you select the <b>staging branch</b> as the branch to build, because we moved our source code from the dev environment to the staging environment and in the script path should be <b>Jenkinsfile</b>e, because Jenkins will look for the Jenkinsfile to be able to build and deploy our application

<img width="984" alt="Screen Shot 2023-12-07 at 07 51 24" src="https://github.com/Big-Zaza/code-challenge/assets/92643385/f13837b1-fdd5-4eca-a3cf-14e7256e8c8f">

Once everything is done, you click on <b>Build now</b> to be able to execute the pipeline. 

And the pipeline should be able to run successfully.

<img width="1080" alt="Screen Shot 2023-12-07 at 07 58 02" src="https://github.com/Big-Zaza/code-challenge/assets/92643385/a8d51c16-3e52-45f2-bed2-303fb0448a73">

To verify if the application has been deployed successfully to your instances, you can try accessing the UI [http://publicip:8080](http://publicip:8080) 

## Monitoring our infrastructure and applications with Prometheus and Grafana

Our goal as a DevOps engineer is to be able to achieve highly available applications and to be able to achieve that, we'll have to ensure that each of our applicatios are thourougly monitored so that before an issue in the application arises or a downtime, we should have already detected it before our users our clients notice it.
That's why we use monitoring tools like Prometheus and Grafana for ensuring the health, performance, and availability of applications and infrastructure.

So in one of our ec2 instances, we'll install and setup the binaries of prometheus using the prometheus_setup.sh script, grafana using the grafana_setup.sh, nodeexporter using the nodeexporter_setup.sh, alertmanager using the alertmanager_setup.sh which can all be found in this repository.

You can also install cadvisor using this command
```bash
cd react-src

```

Once that is done you can access the Prometheus UI via [http://publicip:9090](http://publicip:9090) 

<img width="1435" alt="Screen Shot 2023-12-07 at 09 02 04" src="https://github.com/Big-Zaza/code-challenge/assets/92643385/83ffbfae-d052-48a7-804b-8da7e3003937">


You can access the Grafana UI via [http://publicip:3000](http://publicip:3000) 

<img width="1438" alt="Screen Shot 2023-12-07 at 09 02 24" src="https://github.com/Big-Zaza/code-challenge/assets/92643385/e295942a-a296-4aea-abe2-8a95bef64d32">


You can access the Alertmanager UI via [http://publicip:9093](http://publicip:9093) 

<img width="1413" alt="Screen Shot 2023-12-07 at 09 02 44" src="https://github.com/Big-Zaza/code-challenge/assets/92643385/510f2fcd-ea8b-4f91-9319-8e9eadf9b0e0">

We're going to create some rules using yaml that will define a condition for which an alert should trigger, like for example if our instances or our docker containers have a downtime.
The rules are found in this repo using "instance_monitoring.yaml" and "container_monitoring.yaml"

We're then going to modify the prometheus.yaml so as to configure our node_exporter, alert_manager, cadvisor and as well add our rules.

It's possible that one of our servers can have a downtime and prometheus fires an alert, but we might not notice it because we might be busy working on other things.

That is why we chave to create an alertmanager.yml and configure a communication channel in which those alerts are going to be sent to by the alertmanager so that we might easily notice them. And for this project we used slack as our communication channel since it's one of the most popular collaboaration platforms.

To do it, you need to follow this instructions.

Login to your Slack account and create a Slack channel called alerts.

Create a new Slack app from scratch. Give it a name Prometheus and select a workspace.

Next, we need to enable incoming webhooks. Then add webhook to the workspace.
<img width="691" alt="Screen Shot 2023-12-07 at 10 45 13" src="https://github.com/Big-Zaza/code-challenge/assets/92643385/b39889f4-3a54-40ca-84e1-3158693878ac">

The last thing, we need to copy Webhook URL and use it in Alertmanager config.

Now, update alertmanager.yml config to include a new route to send alerts to the Slack, and you can see that in the alertmanager.yaml

Once everything is done, we can build our dashboard with Grafana for better observability.

<img width="1433" alt="Screen Shot 2023-12-07 at 09 12 51" src="https://github.com/Big-Zaza/code-challenge/assets/92643385/a68d7e05-8fb2-44d0-905f-23d7635a2b6b">


# Bash script to automate the backup process for the MongoDB database to an ec2 instance

```bash
#!/bin/bash

USERNAME=""
PASSWORD=""
ATLAS_CLUSTER_URI=""
# Set the date and time variables
DATE=$(date +"%Y%m%d")
TIME=$(date +"%H%M%S")

# Set the backup directory path
BACKUP_DIR="/home/ubuntu/mongodb_backup"

# Create the backup directory if it doesn't exist
mkdir -p $BACKUP_DIR

# Run mongodump to create the backup
mongodump --uri="mongodb+srv://$USERNAME:$PASSWORD@$ATLAS_CLUSTER_URI/?retryWrites=true&w=majority" --gzip --archive=$BACKUP_DIR/backup_${DATE}_${TIME}.archive
```

And the script caa be configured to run automatically as per the users' wish

From there you can send the archive to an s3 bucket for storage purpose.

```bash
aws s3 cp /path/to/local/file s3://mongo-db-backups/filename-$(date +\%Y\%m\%d_\%H\%M\%S).archive

```

##You can make an ec2 instance unresponsive by;

Blocking all access to outbound traffic, that will make the ec2 instance unresponsive as it will not be able to communicate with any external system or the internet.
And you can resolve it by allowing outbound traffic

