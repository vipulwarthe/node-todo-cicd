# node-todo-cicd

Run these commands:


`sudo apt install nodejs`


`sudo apt install npm`


`npm install`

`node app.js`

=================================================================================

## Complete Step-by-Step Jenkins CICD with GitHub Integration:


link: https://medium.com/@mudasirhaji/complete-step-by-step-jenkins-cicd-with-github-integration-aae3961b6e33

## Prerequties: AWS ec2 instance,Docker,Github,Jenkins

1)create personal access token
2)Jenkins and github integration
3)CI/CD Pipeline
4)create ubuntu 20.04 ec2 instance with the name (Jenkins-master)/t2.micro/sg-SSH/HTTP/HTTPS/Custom TCP:8080/custom TCP:8000-anywhere/8gb storage.

## create personal access token:

-go to the github settings - devloper setting - personal access token - Tokens(classic) - click on generate new token
  
New token : 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  

## First install Jenkins:

-sudo apt-get update
-curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
    /usr/share/keyrings/jenkins-keyring.asc > /dev/null

-echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
    https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
    /etc/apt/sources.list.d/jenkins.list > /dev/null

-sudo apt-get update
-sudo apt-get install fontconfig openjdk-11-jre -y
-java -version
-sudo apt-get install jenkins -y
-sudo systemctl enable jenkins
-sudo systemctl start jenkins
-sudo systemctl status jenkins
-go to browser and paste public ip with 8080 port
-sudo cat /var/lib/jenkins/secrets/initialAdminPassword

## Install docker:

-sudo apt update
-sudo apt install apt-transport-https ca-certificates curl software-properties-common -y
-curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
-echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
-sudo apt update
-sudo apt install docker-ce -y
-sudo systemctl start docker
-sudo systemctl enable docker
-docker --version
-sudo usermod -aG docker $USER

## Create Public & Private key
-ssh-keygen     - enter - enter -enter - created private key
-cd .ssh
-cat id_rsa   (private key)
-cat id_rsa.pub  (public key) if its not working
-cat /root/.ssh/id_rsa.pub  (public key)

## GITHUB settings:
now go to github account in settings - SSH and GPG key - New SSH key - Title -Jenkins-project- paste public key in blank field -Add SSH key - enter authentication token which is received on mobile and verify (now a days not received auth token on mobile )

-go to Jenkins dashboard -new item - name(todo-node-app)-freestyle project
-Genral - descripstion-this is node js todo app - select SCM -git -paste url of git repo - Add credentials - Domain - default - Kind - SSH username with private key - Id -github-jenkins- description - this is for jenkins and github intigration - username - paste instance username(ubuntu)- private key - click enter directly - paste private key - Add - select again credentials option - branch to build - */main -repository browser(auto) - apply and save

now build the project.. you will get the successed output

check on terminal using below cmd you will see jenkins has been integrated with github

-cd /var/lib/jenkins/workspace/todo-node-app
-sudo apt install nodejs -y
-sudo apt install npm -y     (node package manager)
-sudo npm install    

sudo npm audit fix     (if error occurs use this cmd to fix the issue)
-sudo npm audit fix --force (to address all isssue)
-sudo npm audit     (for details) 
-node app.js        (you will see the app is running on 8000)

-go to browser and paste public IP with 8000 port, you will get the output


Now we are going to deploy our app using docker container for that we need to install docker first which is we have already installed;

## Create Dockerfile under same path - 

-cd /var/lib/jenkins/workspace/todo-node-app

FROM node:12.2.0-alpine
WORKDIR app
COPY . .
RUN npm install
EXPOSE 8000
CMD ["node", "app.js"]

-sudo chmod 777 /var/run/docker.sock    -give the permission to docker.sock
-docker build . -t todo-node-app1    (build the image)
-docker images
-docker run -d --name node-todo-new -p 8000:8000 todo-node-app1    (build the container from the same Image)
-docker ps

when we add build steps you will get error for permission:use below cmd for resolve error

-sudo chmod 777 /var/lib/jenkins/workspace/todo-node-app
-sudo usermod -a -G docker jenkins

## Step 7: Configure Web-hook:

first kill the running container using below cmd:

-docker kill “container-id” command.

- go to github repo setting > webhooks > payload url <http://54.221.123.31:8080/github-webhook/ > content type (application/json) > save

go to the jenkins dashboard:

let’s install the GitHub Integration plugin in Jenkins, to integrate it with Webhooks:

Go to Manage Jenkins > Manage Plugins > Available Plugins > Search for “GitHub Integration” > Select Install Without Restart

After adding the plugin we need to add the webhook in our GitHub repo:

Now go to Jenkins > Dashboard > Job > Configure > Build Triggers > Select “GitHub hook trigger for GITScm polling” > Save

Now change the code in GitHub a bit to see if the integration works.

-you will see job will run automatically.
