# Installing Jenkins and Nginx

### Step 1: Add Jenkins Repository list

```

sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
https://pkg.jenkins.io/debian/jenkins.io-2023.key


```

```

echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \
https://pkg.jenkins.io/debian binary/ | sudo tee \
/etc/apt/sources.list.d/jenkins.list > /dev/null

```

### Step 2: Update the package list

```

sudo apt-get update

```

### Step 3: Install Java and Jenkins

```

sudo apt install fontconfig openjdk-17-jre -y
java --version 

sudo apt-get install jenkins -y

```

### Step 4: Enable and Start the Jenkins process

```

sudo systemctl enable jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins | grep active

```

### Step 5: Update the Security Group

>   update the security group for inbound to allow traffic on port 8080, as jenkins runs on port 8080

### Step 6: Get the Jenkins Password

```

sudo cat /var/lib/jenkins/secrets/initialAdminPassword

```

### Step 7: Install Plugins 

>   paste the password -> install suggested plugin

### Step 8: Create Root user 

>  Create username and password

### Step 8: Install Nginx

```

sudo apt-get install nginx -y

```

### Step 9: Start the Nginx

```

sudo systemctl start nginx

```

### Step 10: Check if Service running 

```

sudo systemctl status nginx

```
========================================================

# Setup repo

### Step 1: Create Repository

>   Create repo in github -> Nginx_hello

### Step 2: Create Entry HTML file

>   create index.html (webpage to host on nginx)

```

<!DOCTYPE html>
<html>
<head>
    <title>Hello World</title>
</head>
<body style="background-color:blue">
    <h1>Hello, world!</h1>
    <p>Welcome to my NGINX server.</p>
</body>
</html>

```

### Step 3: Setup webhook for Repo

>   Nginx_hello(repo) -> settings -> Webhooks -> Add webhook -> Payload URL -> http://<your-jenkins-url>/github-webhook/ -> Content type -> application/json -> Just the push event

==========================================================

# Setup Jenkins

### Step 1: Create Credentials for SCM checkout

>    Manage Jenkins -> Credentials -> System -> Add domain -> Domain Name -> Github -> Create

### Step 2: Setup ID/PASSWD

>   Add Credentials -> username -> GithuUsername -> password -> GitToken -> Create -> copy the ID

### Step 3: Create Pipeline

>   on jenkins (New Item) -> Nginx_Webserver -> Pipeline -> ok

### Step 4: Check GITScm polling for webhook

>   GitHub hook trigger for GITScm polling (Checkbox)

### Step 5: Sample Pipeline Script

>   Pipeline Script:

```

pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                git credentialsId: 'CredID', branch: 'main', url: 'repo.git'
            }
        }
        stage('Build') {
            steps {
                sh 'echo "Building..."'
            }
        }
        stage('Deploy') {
            steps {
                sh '''
                sudo cp index.html /var/www/html/
                sudo systemctl restart nginx
                '''
            }
        }
    }
}

```

### Step 6: Change permission for sudoers file for update

```

sudo su 

chmod 740 /etc/sudoers

```

### Step 7: Update the File and add below line in /etc/sudoers

```

sudo vim /etc/sudoers 
jenkins ALL=(ALL) NOPASSWD: /bin/cp, /bin/systemctl  
:wq

```

### Step 8: Revert the permissions for the /etc/sudoers

```

chmod 440 /etc/sudoers

```

### Step 9: First Build

>   do the first build ✨⭐
