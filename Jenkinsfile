pipeline {
  agent any

  // environment {
  //       DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials-id') // Define DockerHub credentials
  //       IMAGE_NAME = 'rahulvivaops/java-gradle-project-demo'
  //       IMAGE_TAG = 'latest'
  // }

  stages {
    
    stage('Build') {
      steps {
        withGradle {
           sh './gradlew clean build --stacktrace -i'
        }
      }
    } // Build

    // stage('Dockerize') {
    //       steps {
    //           script {
    //               docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials-id') {
    //                   def app = docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
    //                   app.push()
    //               }
    //           }
    //       }
    // }

    // stage('Publish') {
    //   steps {
    //     withCredentials([usernamePassword(
    //     credentialsId: 'github-publish-maven', 
    //     passwordVariable: 'MVN_PASSWORD', 
    //     usernameVariable: 'MVN_USERNAME')]) {

    //       withGradle {
    //         sh """
    //           ./gradlew -i --stacktrace publish \
    //               -PMVN_USERNAME=${MVN_USERNAME} \
    //               -PMVN_PASSWORD=${MVN_PASSWORD} \
    //               -PMVN_VERSION=1.${BUILD_NUMBER}
    //         """
    //       }  
    //     }
    //   }
    // } // Publish

    // stage('Post') {
    //   steps {
    //     script {
    //       jacoco()
    //       junit 'lib/build/test-results/test/*.xml'
    //       def pmd = scanForIssues tool: [$class: 'Pmd'], pattern: 'lib/build/reports/pmd/*.xml'
    //       publishIssues issues: [pmd]
    //     }
    //   }
    // } // Post

  }
}
