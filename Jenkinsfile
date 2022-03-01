pipeline {
  agent any
 
  tools {
  maven 'Maven3'
  }
  stages {
    stage ('Build') {
      steps {
      sh 'mvn clean install -f MyWebApp/pom.xml'
      }
    }
    stage ('Code Quality') {
      steps {
        withSonarQubeEnv('SonarQube') {
        sh 'mvn -f MyWebApp/pom.xml sonar:sonar'
        }
      }
    }
    stage ('JaCoCo') {
      steps {
      jacoco()
      }
    }
    stage ('Nexus Upload') {
      steps {
      nexusArtifactUploader(
      nexusVersion: 'nexus3',
      protocol: 'http',
      nexusUrl: 'http://ec2-44-192-113-64.compute-1.amazonaws.com:8081',
      groupId: 'myGroupId',
      version: '1.0-SNAPSHOT',
      repository: 'maven-snapshots',
      credentialsId: 'b02babbf-2dde-4fe3-a963-a558d6f74b38',
      artifacts: [
      [artifactId: 'MyWebApp',
      classifier: '',
      file: 'MyWebApp/target/MyWebApp.war',
      type: 'war']
      ])
      }
    }
    stage ('DEV Deploy') {
      steps {
      echo "deploying to DEV Env "
      deploy adapters: [tomcat9(credentialsId: '6794dd90-7228-4a1b-ad34-2868cbdfd15f', path: '', url: 'http://ec2-35-174-241-104.compute-1.amazonaws.com:8080')], contextPath: null, war: '**/*.war'
      }
    }
    stage ('Slack Notification') {
      steps {
        echo "deployed to DEV Env successfully"
        slackSend(channel:'practice', message: "Job is successful, here is the info - Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
      }
    }
        stage ('DEV Approve') {
      steps {
      echo "Taking approval from DEV Manager for QA Deployment"
        timeout(time: 7, unit: 'DAYS') {
        input message: 'Do you want to deploy?', submitter: 'admin'
        }
      }
    }
     stage ('QA Deploy') {
      steps {
        echo "deploying to QA Env "
        deploy adapters: [tomcat9(credentialsId: '6794dd90-7228-4a1b-ad34-2868cbdfd15f', path: '', url: 'http://ec2-35-174-241-104.compute-1.amazonaws.com:8080')], contextPath: null, war: '**/*.war'
        }
    }
    stage ('QA Approve') {
      steps {
        echo "Taking approval from QA manager"
        timeout(time: 7, unit: 'DAYS') {
        input message: 'Do you want to proceed to PROD?', submitter: 'admin,manager_userid'
        }
      }
    }
        stage ('Slack Notification for QA Deploy') {
      steps {
        echo "deployed to QA Env successfully"
        slackSend(channel:'practice', message: "Job is successful, here is the info - Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
      }
    }  
  }
}
// node {

//     def mvnHome = tool 'Maven3'
//     stage ("checkout")  {
//        checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: '3d81a82c-f48b-408a-9ddc-586e94e2b74c', url: 'https://github.com/EdenAyu/myFirstRepo']]])
//     }

//    stage ('build')  {
//     sh "${mvnHome}/bin/mvn clean install -f MyWebApp/pom.xml"
//     }

//      stage ('Code Quality scan')  {
//        withSonarQubeEnv('SonarQube') {
//        sh "${mvnHome}/bin/mvn -f MyWebApp/pom.xml sonar:sonar"
//         }
//    }
  
//    stage ('Code coverage')  {
//        jacoco()
//    }

//    stage ('Nexus upload')  {
//         nexusArtifactUploader(
//         nexusVersion: 'nexus3',
//         protocol: 'http',
//         nexusUrl: 'http://ec2-44-192-113-64.compute-1.amazonaws.com:8081',
//         groupId: 'myGroupId',
//         version: '1.0-SNAPSHOT',
//         repository: 'maven-snapshots',
//         credentialsId: 'b02babbf-2dde-4fe3-a963-a558d6f74b38',
//         artifacts: [
//             [artifactId: 'MyWebApp',
//              classifier: '',
//              file: 'MyWebApp/target/MyWebApp.war',
//              type: 'war']
//         ]
//      )
//     }
   
//    stage ('DEV Deploy')  {
//       echo "deploying to DEV Env "
//       deploy adapters: [tomcat9(credentialsId: '6794dd90-7228-4a1b-ad34-2868cbdfd15f', path: '', url: 'http://ec2-35-174-241-104.compute-1.amazonaws.com:8080')], contextPath: null, war: '**/*.war'

//     }

//   stage ('Slack notification')  {
//     slackSend(channel:'practice', message: "Job is successful, here is the info -  Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
//    }

//    stage ('DEV Approve')  {
//             echo "Taking approval from Manager for QA Deploy"     
//             timeout(time: 7, unit: 'DAYS') {
//             input message: 'Do you approve to deploy into QA environment?', submitter: 'admin'
//             }
//      }

// stage ('QA Deploy')  {
//      echo "deploying into QA Env " 
// deploy adapters: [tomcat9(credentialsId: '6794dd90-7228-4a1b-ad34-2868cbdfd15f', path: '', url: 'http://ec2-35-174-241-104.compute-1.amazonaws.com:8080')], contextPath: null, war: '**/*.war'

// }

// stage ('QA Approve')  {
//     echo "Taking approval from QA manager"
//     timeout(time: 7, unit: 'DAYS') {
//         input message: 'Do you want to proceed to PROD Deploy?', submitter: 'admin,manager_userid'
//   }
// }

// stage ('PROD Deploy')  {
//      echo "deploying into PROD Env " 
// deploy adapters: [tomcat9(credentialsId: '6794dd90-7228-4a1b-ad34-2868cbdfd15f', path: '', url: 'http://ec2-35-174-241-104.compute-1.amazonaws.com:8080')], contextPath: null, war: '**/*.war'

// }
// }
