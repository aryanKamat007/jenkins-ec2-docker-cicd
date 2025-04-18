## _🚀 Jenkins CI/CD Setup on AWS EC2 with Docker_

This project demonstrates a basic **CI/CD pipeline** using _Jenkins on an AWS EC2 instance_. It uses _SSH key-based GitHub integration_ and a _Docker image to build and run the app_. Every push to the GitHub repository automatically triggers a Jenkins build, which pulls the latest code, builds it using Docker, and deploys it on a specified port.

## _🧰 Tools Used_

- 🖥️ _Jenkins_ (installed on an EC2 Ubuntu instance)
- 🐳 _Docker_ (used inside the Jenkins job to build and run the app)
- 🔐 _SSH_ (for secure GitHub access)
- ☁️ _GitHub Webhook_ (to trigger builds on push)

## _📦 Jenkins Job Overview_

- _Type_: Freestyle Project
- _Source Code_: Cloned using SSH
- _Build Environment_: Docker container
- _Trigger_: GitHub webhook (on push)
- _Post-Build Action_: Run the app and expose it on a port

## _🛠️ Setup Instructions_

### _1. Launch EC2 Instance_

1. **Create an EC2 instance** on AWS with Ubuntu (choose your preferred version, e.g., Ubuntu 20.04).
2. **Security Group**:
   - Open port `8080` for Jenkins UI (so you can access Jenkins on `http://<EC2-PUBLIC-IP>:8080`).
   - Open the app's port (e.g., `3000`, `5000`, or any port your app will run on).
3. **SSH Access**:
   - Ensure your EC2 instance has a key pair for SSH access. You'll need to use this key pair to connect to the instance.
  
   Once the EC2 instance is launched, SSH into it:
   ```bash
   ssh -i <your-key-pair>.pem ubuntu@<EC2-PUBLIC-IP>


### _2. Install Jenkins and Docker_
Install Jenkins and Docker on your EC2 Instance:

 1. Update the EC2 instance:
    ```bash
    sudo apt update
 2. Install Java (required for Jenkins):
    ```bash
    sudo apt install openjdk-11-jdk -y
 3. Install Docker:
    ```bash
    sudo apt install docker.io -y  
 4. Add Jenkins repository key and repository:
    ```bash
    wget -q -O - https://pkg.jenkins.io/keys/jenkins.io.key | sudo apt-key add -
    sudo sh -c 'echo deb http://pkg.jenkins.io/debian/ stable main > /etc/apt/sources.list.d/jenkins.list'
 5. Install Jenkins:
    ```bash
    sudo apt update
    sudo apt install jenkins -y   
 6. Start and enable Jenkins:
    ```bash
    sudo systemctl start jenkins
    sudo systemctl enable jenkins
7. Access Jenkins UI in browser:
   Open your browser and navigate to `http://<your-ec2-public-ip>:8080` to complete the setup process.
   - **Retrieve the Jenkins initial setup password**:
    ```bash
    sudo cat /var/lib/jenkins/secrets/initialAdminPassword
    ```
   - **Once unlocked, Jenkins will prompt you to install suggested plugins. Click Install suggested plugins.**


### _3. Generate SSH Keys and Add to GitHub_

  1. Generate SSH keys for Jenkins to access GitHub securely:
     ```bash
     ssh-keygen
      ```
     This will generate a private (id_rsa) and public (id_rsa.pub) key in the ~/.ssh/ directory.

  2. Copy the public key to GitHub:
     ```bash
     cat ~/.ssh/id_rsa.pub
     ```
     - **Go to your GitHub repository → Settings → Deploy Keys → Add Key**
     - **Paste the public key and give it write access.**

  3. Add SSH private key to Jenkins:

   - **In Jenkins → Manage Jenkins → Manage Credentials → (Global credentials) → Add Credentials**

   - **Choose SSH Username with private key, use git as the username, and paste the private key content (~/.ssh/id_rsa).**


### _4. Access Jenkins UI to Create and Configure the Job_

 1. Access Jenkins UI:
    - **Go to http://<EC2-PUBLIC-IP>:8080 in your browser to open Jenkins.**.

 2. Create the Jenkins Job:
    - **After the setup, click on "Create New Job."**
    - **Choose Freestyle Project, name it, and click "OK."**
   
### _5. Configure GitHub Webhook_

 1. In your GitHub repository, go to Settings → Webhooks → Add Webhook.

 2. In the webhook form, fill out the following:
    - **Payload URL: http://<EC2-IP>:8080/github-webhook/**
    - **Content type: application/json**
    - **Which events would you like to trigger this webhook? Select "Just the push event."**

### _6. Jenkins Freestyle Job Configuration_
  1. Source Code Management:
    - **In Jenkins job configuration, under "Source Code Management", select Git.**
    - **Enter your GitHub repository URL (SSH) and choose the credentials you added earlier.**

  2. Build Triggers:
    - **Under "Build Triggers", check GitHub hook trigger for GITScm polling.**

  3. Build Environment:
     Add a build step to use Docker:

     ```bash
        docker build -t my-app-image .
        docker run -d --name my-app-container -p 3000:3000 my-app-image
     ```
     
### _7.✅ Final Result_
  - **Every push to GitHub triggers Jenkins via webhook**
  - **Jenkins pulls the latest code and builds it inside Docker**
  - **The app is deployed and becomes accessible on the specified port (e.g., http://<EC2-IP>:3000)**
