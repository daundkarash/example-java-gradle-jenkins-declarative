pipeline {
    agent {
        kubernetes {
            yaml """
            apiVersion: v1
            kind: Pod
            spec:
              containers:
              - name: docker
                image: docker:20.10.8-dind
                securityContext:
                  privileged: true
                command:
                - dockerd-entrypoint.sh
                args:
                - --host=tcp://0.0.0.0:2375
                - --host=unix:///var/run/docker.sock
                env:
                - name: DOCKER_TLS_CERTDIR
                  value: ""
                volumeMounts:
                - name: docker-graph-storage
                  mountPath: /var/lib/docker
              volumes:
              - name: docker-graph-storage
                emptyDir: {}
            """
        }
    }

    stages {
        stage('Check Docker') {
            steps {
                container('docker') {
                    sh 'docker --version'
                }
            }
        } // Check Docker

        stage('Build') {
            steps {
                // Run the Gradle build command in the Docker container
                    sh './gradlew clean build --stacktrace -i'
            }
        } // Build

        stage('Docker Build') {
            steps {
                // Build the Docker image
                container('docker') {
                    sh 'docker build -t java-application:latest .'
                }
            }
        } // Docker Build

        // Uncomment and use the following stage if you want to push the image to Docker Hub
        /*
        stage('Dockerize') {
            steps {
                container('docker') {
                    script {
                        docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials-id') {
                            def app = docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                            app.push()
                        }
                    }
                }
            }
        } // Dockerize
        */

        // Uncomment and use the following stage if you want to publish the built JAR to a Maven repository
        /*
        stage('Publish') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'github-publish-maven', 
                    passwordVariable: 'MVN_PASSWORD', 
                    usernameVariable: 'MVN_USERNAME')]) {

                    sh """
                        ./gradlew -i --stacktrace publish \
                            -PMVN_USERNAME=${MVN_USERNAME} \
                            -PMVN_PASSWORD=${MVN_PASSWORD} \
                            -PMVN_VERSION=1.${BUILD_NUMBER}
                    """
                }  
            }
        } // Publish
        */

        // Uncomment and use the following stage for post-build actions like running tests and reporting
        /*
        stage('Post') {
            steps {
                script {
                    jacoco()
                    junit 'lib/build/test-results/test/*.xml'
                    def pmd = scanForIssues tool: [$class: 'Pmd'], pattern: 'lib/build/reports/pmd/*.xml'
                    publishIssues issues: [pmd]
                }
            }
        } // Post
        */
    }
}
