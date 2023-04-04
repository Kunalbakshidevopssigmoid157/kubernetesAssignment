# kubernetesAssignment

# We have one java web project folder. And the folder contains the all depenedecies , libraries,configuration files and source code & test files in developer site/development server. Then we decide to deploy the project in operation site/operstion server.

# We can do this with help of maven. Maven is the project management tools which only java projects. Maven is create type of compress file : jar (java archieve) and war (web archieve).

Then we first inatall the maven tools.

Steps Installization of Maven 

Install Java
Maven 3.3 or greater requires JDK 1.7 or above to be installed. We will install OpenJDK, which is the default Java development and runtime in Ubuntu 18.04.

#Install OpenJDK:

sudo apt-get install -y default-jdk

#Verify the Java version:
java -version

#You can download the latest stable version of Apache Maven from the official website:
sudo wget https://www-us.apache.org/dist/maven/maven-3/3.9.1/binaries/apache-maven-3.9.1-bin.tar.gz

#Once the download has completed, extract the downloaded archive:
sudo tar -xvzf apache-maven-3.9.1-bin.tar.gz

#Next, rename the extracted directory:
sudo mv apache-maven-3.6.0 maven 

Setup environment variables

#Next, you will need to setup the environment variables such as M2_HOME, JAVA_HOME and PATH. You can do this by creating a mavenenv.sh file inside of the /etc/profile.d/ directory:

sudo vi /etc/profile.d/mavenenv.sh
#Add the following lines:

export JAVA_HOME=/usr/lib/jvm/default-java

export M2_HOME=/opt/maven

export PATH=${M2_HOME}/bin:${PATH}

#Save and close the file, and make it executable:
sudo chmod +x /etc/profile.d/mavenenv.sh

#Now you can load the environment variables:
source /etc/profile.d/mavenenv.sh

#Verify installation
#Once everything has been successfully configured, check the version of Apache Maven:
mvn --version
#After that we compress this project into war.Then we go the project folder/directory.
# Simple we compress project file into war using below command.
mvn package

#Then we create the dockerfile of this project.
touch dockerprojectfile
vi dockerprojectfile
#Add the following lines:

FROM gcr.io/kodekloud/centos-ssh-enabled:tomcat
#



COPY /target/docker-java-sample-webapp-1.0-SNAPSHOT.war /usr/local/tomcat/webapps/
#Copy the war file into webapps in tomcat 



EXPOSE 8080
#To give the port to tomcat server

#Then we create the image of this dockerprojectfile
docker build . -f dockerprojectfile -t k8s_pro

#Then we upload this image the dockerhub
#First go the login the dockerhub
docker login
#Then give user credentials

#tag this dockerprojectfile image
docker tag k8s_pro:latest kunalsigmoid157/k8s_pro

#Push the image in dockehub repository
docker push kunalsigmoid157/demodockerimage:kunalsigmoid157/k8s_pro

#Then we create the dockerfile of this project.
touch deployment
vi deployment.yml
#Add the following lines:
apiVersion: apps/v1
kind: Deployment
metadata:
  name: tomcat-deployment
  labels: 
    purpose: deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: tomcat-deploy
  template:
    metadata:
      name: tomcat-dep
      labels:
        app: tomcat-deploy
    spec:
      containers:
      - name: tomcat
        image: kunalsigmoid157/k8s_pro:latest 
        ports:
        - containerPort: 8080



#Then we create the dockerfile of this project.
touch service.yml
vi service.yml
#Add the following lines:
apiVersion: v1
kind: Service
metadata:
  name: tomcat-service
  labels:
    purpose: service
spec:
  selector:
    app: tomcat-deploy
  ports:
  - port: 8080
    targetPort: 8080
    nodePort: 30865
  type: NodePort

Steps to Install Kubernetes 
Install VirtualBox Hypervisor
#As mentioned above, you need a virtual machine in which you can set up your single node cluster with Minikube. Depending on your preference, you can use VirtualBox or KVM.

This guide will show you how to install Minikube with VirtualBox.
# To install VirtualBox on Ubuntu, run the command:
sudo apt install virtualbox virtualbox-ext-pack

#Terminal VirtualBox installation process in the Linux terminal.
Confirm the installation with y and hit Enter.

#Next, the licence agreement appears on the screen. Press Tab and then Enter to continue.

VirtualBox licence agreement.
4. The installer asks you to agree with the terms of the VirtualBox PUEL license by selecting Yes.

Setting up virtualbox-ext-pack.
5. Wait for the installation to complete and then move on to the next step.

Note: For the VirtualBox hypervisor to work, hardware virtualization must be enabled in your system BIOS.

Step 3: Install Minikube
With VirtualBox set up, move on to installing Minikube on your Ubuntu system.

1. First, download the latest Minikube binary using the wget command:

wget https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64

Downloading the Minikube binary to install Minikube on Ubuntu 18.04.

2. Copy the downloaded file and store it into the /usr/local/bin/minikube directory with:

sudo cp minikube-linux-amd64 /usr/local/bin/minikube

There will be no output if the command was executed correctly.

3. Next, give the file executive permission using the chmod command:

sudo chmod 755 /usr/local/bin/minikube

Again, there will be no output.

Move Minikube file and give it executive permission.
4. Finally, verify you have successfully installed Minikube by checking the version of the software:

minikube version

The output should display the version number of the software, as in the image below.

Check Minikube version.
Step 4: Install Kubectl
To deploy and manage clusters, you need to install kubectl, the official command line tool for Kubernetes.

1. Download kubectl with the following command:

curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl

Download Kubectl binary on Ubuntu.
2. Make the binary executable by typing:

chmod +x ./kubectl

3. Then, move the binary into your path with the command:

sudo mv ./kubectl /usr/local/bin/kubectl

4. Verify the installation by checking the version of your kubectl instance:

kubectl version -o json

Step 5: Start Minikube
Once you have set up all the required software, you are ready to start Minikube.

Run the following command:

minikube start

First, the system downloads the Minikube ISO file from an online source and the localkube binary. Then, it creates a virtual machine in VirtualBox within which it starts and configures a single node cluster.

The command to start Minikube on Ubuntu.
Managing Kubernetes with Minikube
Common Minikube Commands
To see the kubectl configuration use the command:

kubectl config view

Image of using the kubectl config view command.
To show the cluster information:

kubectl cluster-info


**Run the Kubernets for this java web project**
kubectl get deployments
 
 kubectl get service
 
 kubectl create -f deployment tomcat-deployment
 
 kubectl create -f deployment.yml
 
 kubectl create -f service.yml
 
 kubectl get service
 
 minikube service tomcat-service --url
