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
- GitHub API
- GitHub plugin

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


## Docker compose
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

## Standalone tests (Execute shell)

### Test #1
- Input: `echo hello world`
- Output: Hello world

### Test #2
- Input: `echo "Current date and time is $(date)"`
- Output: Current date and time is Mon Sep 13 01:37:16 UTC 2021

### Test #3
- Input:
  ```
  NAME=Jefté echo "Hello, $NAME. Current date and time is $(date)"
  ```
- Output: Hello, Jefté. Current date and time is Mon Sep 13 01:42:20 UTC 2021

### Test #4
- Input:
  ```
  NAME=Jefté echo "Hello, $NAME. Current date and time is $(date) > /tmp/info"
  ```
- Output: Hello, Jefté. Current date and time is Mon Sep 13 01:44:33 UTC 2021

### Test #5
- File:
  ```
  script.sh
  #!/bin/bash


  NAME=$1
  LASTNAME=$2

  echo "Hello, $NAME $LASTNAME"
  ```
- Input: `/tmp/script.sh Jefté Goes`
- Output: Hello, Jefté Goes

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
  NAME=Jefté
  LASTNAME=Goes
  /tmp/script.sh $NAME $LASTNAME
  ```
- Output: Hello, Jefté Goes

### Test #7 (This project is parameterized)
- Parameters (String):
  - FIRST_NAME: Jefté
  - SECOND_NAME: Goes
- Input: `echo "Hello, $FIRST_NAME $SECOND_NAME"`
- Output: Hello, Jefté Goes

### Test #8 (This project is parameterized)
- Parameters (String):
  - FIRST_NAME: Jefté
  - SECOND_NAME: Goes
- Parameters (Choice):
  - LASTNAME:
    - Silva
    - **Oliveira**
    - Souza
- Input: `echo "Hello, $FIRST_NAME $SECOND_NAME $LASTNAME"`
- Output: Hello, Jefté Goes Oliveira

### Test #9 (This project is parameterized)
- Parameters (String):
  - FIRST_NAME: Jefté
  - SECOND_NAME: Goes
- Parameters (Choice):
  - LASTNAME:
    - Silva
    - **Oliveira**
    - Souza
- Input: `echo "Hello, $FIRST_NAME $SECOND_NAME $LASTNAME"`
- Output: Hello, Jefté Goes Oliveira

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
  - FIRST_NAME: Jefté
- Parameters (Choice):
  - LASTNAME:
    - **Silva**
    - Oliveira
    - Souza
#### 1
- Parameters (Boolean):
  - SHOW: true
- Input: `/tmp/script.sh $FIRST_NAME $LASTNAME $SHOW`
- Output: Hello, Jefté Silva
#### 2
- Parameters (Boolean):
  - SHOW: false
- Input: `/tmp/script.sh $FIRST_NAME $LASTNAME $SHOW`
- Output: If you want to see the name, please mark the show option





- 
# Config email

#
#