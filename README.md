# Solution of Code Challenge

This repo contains the source code to be able to provision and deploy an Infrastructure with a CI/CD pipeline deployed.


![Node Version](https://img.shields.io/badge/node-v20+-green.svg)
![NPM Version](https://img.shields.io/badge/npm-v9+-blue.svg)
![MongoDB Version](https://img.shields.io/badge/mongodb-v7+-yellowgreen.svg)
![Mongoose Version](https://img.shields.io/badge/mongoose-v7+-red.svg)

Demo: [https://mern-crud-mpfr.onrender.com](https://mern-crud-mpfr.onrender.com)

![MERN CRUD Screenshot](screenshot.png)


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

## Monitoring

There's 
If you want to modify the front-end, go to *react-src* folder via the terminal.

```bash
cd react-src

```

Install the dependencies required by React.
```bash
npm install
```

Run the *development server* for React.
```bash
REACT_APP_API_URL=http://localhost:3000 npm start
```

View [http://localhost:4200](http://localhost:4200) on the browser.

To make a production build, simply run on *react-src* folder via the terminal.
```bash
npm run build
```

It re-creates a folder named *public* on the root directory. This is where the production-ready front-end of the web application resides.

## Docker
```bash
docker-compose up
```

## Contribute
Feel free to help out as I may have other work/life commitments. See [CONTRIBUTING.md](CONTRIBUTING.md).

## To Do

- [x] Create
- [x] Read
- [x] Update
- [x] Delete
- [x] Real-time broadcast using Socket.io
- [x] Deploy in Heroku
- [x] Front-end validation (HTML)

## License
**MERN CRUD** is available under the **MIT** license. See the [LICENSE](LICENSE) file for more info.
