# What is Jenkins?

- Jenkins is a self-contained, open source automation server which can be used to automate all sorts of tasks such as building, testing, and deploying software.

- Jenkins can be installed through native system packages, Docker, or even run standalone by any machine with the Java Runtime Environment installed.

# CI/CD Definitions

# Initial configuration
## Plugins 
- Mailer plugin
- Job DSL https://plugins.jenkins.io/job-dsl/
  - Example of DSL script for create job
    ```
    job('job_dsl_created') {
      description('This is my awesome Job')

      parameters {
        stringParam('Planet', defaultValue = 'word', description = 'This is the world')
        booleanParam('FLAG', true)
        choiceParam('OPTION', ['option 1 (default)', 'option 2', 'option 3'])
      }

      scm {
        git('https://github.com/jenkins-docs/simple-java-maven-app.git', 'master')
        /*git('https://github.com/jenkins-docs/simple-java-maven-app.git', 'master',{node -> node / 'extensions' << '' }) to Jenkins disable tag every build in dsl*/
      }

      triggers {
        cron('H 5 * * 7')
      }

      steps {
        shell("echo 'Hello world'")
        shell("""
                echo 'Hello world with multiple lines'
                echo 'Running script'
              """)
      }

      publishers {
        mailer('jefte.goes@example.com', true, true)
      }
    }
    ```
- Git client plugin
- Git plugin
- Docker plugin
- GitHub API
- Publish Over FTP
- GitHub plugin
- Pipeline
  - Example of script **declarative** pipeline
    ```
    pipeline {
        agent any 
        stages {
            stage('Build') { 
                steps {
                    echo "Building..."
                }
            }
            stage('Test') { 
                steps {
                    echo "Testing..."
                }
            }
            stage('Deploy') { 
                steps {
                    echo "Deploying..."
                }
            }
        }
    }
    ```
  - Example of script with `sh` command
    ```
    pipeline {
        agent any 
        stages {
            stage('Build') { 
                steps {
                    sh 'echo "My first pipeline"'
                    sh '''
                        echo "By the way, I can do more stuff in here"
                        ls -lah
                    '''
                }
            }
        }
    }
    ```
  - Example of script with **retry** command, in this example the jekins will display error three times, because missing `echo` command 
    ```
    pipeline {
        agent any 
        stages {
            stage('Timeout') { 
                steps {
                    retry(3) {
                        sh 'I am not going to work :c'    
                    }
                }
            }
        }
    }
    ```
  - Example of script with **timeout**, in this example the jekins will display error, because timeout has been exceeded
    ```
    pipeline {
        agent any 
        stages {
            stage('Timeout') { 
                steps {
                    retry(3) {
                        sh 'echo hello'    
                    }
                    
                    timeout(time: 3, unit: 'SECONDS') {
                        sh 'sleep 5'
                    }
                }
            }
        }
    }
    ```
  - Example of script with **environment variables**
    ```
    pipeline {
        agent any 
        
        environment {
            NAME =  'Jeft??'
            LASTNAME = 'Goes'
        }
        
        stages {
            stage('Build') { 
                steps {
                    sh 'echo $NAME $LASTNAME'
                }
            }
        }
    }
    ```
  - Example of script with **credentials**
    ```
    pipeline {
        agent any 
        
        environment {
            secret = credentials('SECRET_TEXT')
        }
        
        stages {
            stage('Build') { 
                steps {
                    sh 'echo $secret'
                }
            }
        }
    }
    ```
  - Example of script with **post-actions**, in this example the jekins will display failure, because command `exit 1`
    ```
    pipeline {
        agent any 
        
        stages {
            stage('Test') { 
                steps {
                    sh 'echo "Fail!"; exit 1'
                }
            }
        }
        post { 
            always {
                echo 'I will always get executed :D'
            }
            success {
                echo 'I will only get executed if this success'
            }
            failure {
                echo 'I will only get executed if this fails'
            }
            unstable {
                echo 'I will only get executed if this is unstable'
            }
        }
    }
    ```
- MSBuild Plugin
  - So very hard to install MSbuild/Mono on ubuntu... Xbuild be deprecated, to build applications with .NET Framework
  
## Configuration
- Docker (jenkins/jenkins) / Linux (Debian)

## Create First Admin User (Default)
- Username: admin
- Password: 1234
- Confirm password: 1234
- Full name: Jenkins Admin
- jenkins@jenkins.com

## Tips & tricks
### Example global enviroment variables in Jenkins (Execute shell)
  ```
  echo "BUILD NUMBER FOR THIS IS $BUILD_NUMBER"
  echo "BUILD ID IS $BUILD_ID"
  echo "BUILD URL IS $BUILD_URL"
  echo "JOB NAME IS $JOB_NAME"
  ```

### Example custom enviroment variables in Jenkins (Execute shell)
- To enable > Manage Jenkins > Configure System > Global properties > Mark option Environment variables
  ```
  echo "THE NAME OF ADMINISTRATOR IS $NAME_OF_THE_ADMINISTRATOR"
  echo "THE COUNTRY OF SERVER INSTALLED IS $COUNTRY"
  ```

### Jenkins cron (Schedules) https://crontab.guru/
- Example every 1 minute: `* * * * *`


## Docker compose (docker-compose.yml)
```
version: '3'
services:
  jenkins:
    container_name: jenkins_dev
    image: jenkins/jenkins
    ports:
      - "8080:8080"
    volumes:
      - "./jenkins_home:/var/jenkins_home"
    networks:
      - net
networks:
  net:
```

## Dockerfile docker into jenkins image
  ```
  FROM jenkins/jenkins

  USER root

  # Install docker
  RUN apt-get update && \
      apt-get -y install \
      apt-transport-https \
      ca-certificates \
      curl \
      gnupg \
      lsb-release

  RUN curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

  RUN echo \
    "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian \
    $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null

  RUN apt-get update
  RUN apt-get -y install docker-ce docker-ce-cli containerd.io

  # Install docker-compose
  RUN curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

  RUN chmod +x /usr/local/bin/docker-compose

  USER jenkins
  ```

## Standalone tests freestyle project (Execute shell)
### Test #1
- Input: `echo hello world`
- Output: Hello world

### Test #2
- Input: `echo "Current date and time is $(date)"`
- Output: Current date and time is Mon Sep 13 01:37:16 UTC 2021

### Test #3
- Input:
  ```
  NAME=Jeft?? echo "Hello, $NAME. Current date and time is $(date)"
  ```
- Output: Hello, Jeft??. Current date and time is Mon Sep 13 01:42:20 UTC 2021

### Test #4
- Input:
  ```
  NAME=Jeft?? echo "Hello, $NAME. Current date and time is $(date) > /tmp/info"
  ```
- Output: Hello, Jeft??. Current date and time is Mon Sep 13 01:44:33 UTC 2021

### Test #5
- File:
  ```
  script.sh
  #!/bin/bash


  NAME=$1
  LASTNAME=$2

  echo "Hello, $NAME $LASTNAME"
  ```
- Input: `/tmp/script.sh Jeft?? Goes`
- Output: Hello, Jeft?? Goes

### Test #6
- File script.sh:
  ```
  #!/bin/bash


  NAME=$1
  LASTNAME=$2

  echo "Hello, $NAME $LASTNAME"
  ```
- Input:
  ```
  NAME=Jeft??
  LASTNAME=Goes
  /tmp/script.sh $NAME $LASTNAME
  ```
- Output: Hello, Jeft?? Goes

### Test #7 (This project is parameterized)
- Parameters (String):
  - FIRST_NAME: Jeft??
  - SECOND_NAME: Goes
- Input: `echo "Hello, $FIRST_NAME $SECOND_NAME"`
- Output: Hello, Jeft?? Goes

### Test #8 (This project is parameterized)
- Parameters (String):
  - FIRST_NAME: Jeft??
  - SECOND_NAME: Goes
- Parameters (Choice):
  - LASTNAME:
    - Silva
    - **Oliveira**
    - Souza
- Input: `echo "Hello, $FIRST_NAME $SECOND_NAME $LASTNAME"`
- Output: Hello, Jeft?? Goes Oliveira

### Test #9 (This project is parameterized)
- Parameters (String):
  - FIRST_NAME: Jeft??
  - SECOND_NAME: Goes
- Parameters (Choice):
  - LASTNAME:
    - Silva
    - **Oliveira**
    - Souza
- Input: `echo "Hello, $FIRST_NAME $SECOND_NAME $LASTNAME"`
- Output: Hello, Jeft?? Goes Oliveira

### Test #10 (This project is parameterized)
- File script.sh:
  ```
  #!/bin/bash


  NAME=$1
  LASTNAME=$2
  SHOW=$3

  if [ "$SHOW" = "true" ]; then
    echo "Hello, $NAME $LASTNAME"
  else
    echo "If you want to see the name, please mark the show option"
  fi
  ```
- Parameters (String):
  - FIRST_NAME: Jeft??
- Parameters (Choice):
  - LASTNAME:
    - **Silva**
    - Oliveira
    - Souza
#### 1
- Parameters (Boolean):
  - SHOW: true
- Input: `/tmp/script.sh $FIRST_NAME $LASTNAME $SHOW`
- Output: Hello, Jeft?? Silva
#### 2
- Parameters (Boolean):
  - SHOW: false
- Input: `/tmp/script.sh $FIRST_NAME $LASTNAME $SHOW`
- Output: If you want to see the name, please mark the show option


# Timezone
1. Manage Jenkins > Manage Users > $User > Configure > User Defined Time Zone
  - Time Zone = Brazil/East

# Parallel

# Agents

# Reporting

# Backup

# Multibranch pipeline
- The Multibranch Pipeline project type enables implement different Jenkinsfile for different branches of the same project. In a Multibranch Pipeline project, Jenkins automatically discovers, manages and executes Pipelines for branches which contain a Jenkinsfile in source control.

# Nodes

# Jenkins Pipeline check-in pipeline in Git
1. Create a repo into github private or public, with name Jenkinsfile with respective pipeline
2. Create pipeline job
3. In section **Pipeline**
   1. Definition = **Piepline script form SCM**
   2. SCM = Git
   3. Repository url = Repo .git here
   4. Credentials, if repo is private, choice credential with username and password (token) # https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/ 
      1. Manage Jenkins > Credentials > Username with password


# Email
1. Email is really important notification to team!
2. Creating Freestyle / Pipelines projects
3. Build + Testing + reporting the projects

## Common configuration
1. Manage Jenkins > Configure System > Jenkins Location
   1. Jenkins URL = http://XXXXXXXXXXXXXXXXXXX:8080/
   2. System Admin e-mail address = Notifications

## Config: E-mail Notification (To pipeline step mail: Mail)
2. Manage Jenkins > Configure System > E-mail Notification
3. Configure email with gmail
   - SMTP server = smtp.gmail.com
   - SMTP Username = xxxxxxxxx@gmail.com
   - SMTP Password = $$$$$$$$$$
   - Use SSL = Unmark SSL
   - Use TLS = Mark TLS
   - SMTP port = 587 (Port of the TLS)
   - Save the configuration

## Config: Extended E-mail Notification (To pipeline step emailext: Extended Email)
2. Manage Jenkins > Configure System > E-mail Notification
3. Configure email with gmail
   - SMTP server = smtp.gmail.com
   - SMTP Username = xxxxxxxxx@gmail.com
   - SMTP Password = $$$$$$$$$$
   - Use SSL = Unmark SSL
   - Use TLS = Mark TLS
   - SMTP port = 587 (Port of the TLS)
   - Save the configuration

# Working with Jenkins for .Net applications
## To compile and test the application we has two approches for differrent .Net Frameworks
- For .Net Framework 4.X we need this tools:
  - Operational system Windows, for Linux we need to Mono, but is so hard...
  - .Net Framework
  - MsBuild
    - Plugin jenkins https://plugins.jenkins.io/msbuild/
    - Build engine (Installed in the machine) https://docs.microsoft.com/en-us/visualstudio/msbuild/msbuild?view=vs-2019
  - Nuget https://www.nuget.org/downloads
  - NUnit https://nunit.org/download/
- .Net Core / .net 5
  - Operational system Windows or Linux
  - Dotnet cli


## First cenario: Dockerfile + Jenkins (linux/docker) + Donet Core Directly + Pipeline + Ftp
```
pipeline {
    agent any
    
    stages {
        stage('Clone') {
            steps {
                echo "Clone repository..."
                git(
                    url: 'https://github.com/jeftegoes/ExampleUnitTestWithXUnit.git',
                    branch: 'master'
                )
            }
        }
        stage('Restore packages') {
            steps {
                sh 'dotnet restore'
            }
        }
        stage('Clean') {
            steps {
                sh 'dotnet clean'
            }
        }
        stage('Build') {
            steps {
                sh 'dotnet build'
            }
        }
        stage('Test') {
            steps {
                sh 'dotnet test'
            }
        }
        stage('Publish') {
            steps {
                sh 'dotnet publish -c Release'
            }
        }
        stage('Email') {
            steps {
                emailext body: '<b>Status: $BUILD_STATUS</b><br>Project: $PROJECT_NAME <br>Build Number: $BUILD_NUMBER <br> URL de build: $BUILD_URL', subject: '$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS!', to: 'jefte.goes@hotmail.com'
            }
        }
        stage('Deploy') {
            steps {
                ftpPublisher alwaysPublishFromMaster: true,
                 continueOnError: false,
                 failOnError: false,
                 masterNodeName: '',
                 paramPublish: null,
                 publishers: [[configName: 'MY_CONFIG_HERE', transfers: [[asciiMode: false, cleanRemote: true, excludes: '', flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: 'REMOTE_PATH_HERE', remoteDirectorySDF: false, removePrefix: 'Business/bin/Release/net5.0/publish', sourceFiles: 'Business/bin/Release/net5.0/publish/*.*']], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false]]
            }
        }
    }
    
    post {
        always {
            archiveArtifacts artifacts: 'Business/bin/Release/net5.0/publish/*.*', allowEmptyArchive: true
        }
        success {
            echo 'Success'
        }
        failure {
            echo 'Failure, send email notification...'
            mail bcc: '', body: "<b>Example</b><br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "ERROR CI: Project name -> ${env.JOB_NAME}", to: "jefte.goes@hotmail.com";
        }
    }
}
```

## Second cenario: Dockerfile + Jenkins (linux/docker) + Docker + Pipeline + Remotehost (SSH)