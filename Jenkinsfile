pipeline {
    agent {
        kubernetes {
            yaml """
            apiVersion: v1
            kind: Pod
            spec:
              containers:
              - name: podman
                image: quay.io/podman/stable:latest
                securityContext:
                  privileged: true
                command:
                - sleep
                args:
                - infinity
                volumeMounts:
                - name: podman-graph-storage
                  mountPath: /var/lib/containers

              - name: snyk
                image: snyk/snyk:alpine
                command:
                - sleep
                args:
                - infinity
                volumeMounts:
                - name: podman-graph-storage
                  mountPath: /var/lib/containers
              
              volumes:
              - name: podman-graph-storage
                emptyDir: {}
            """
        }
    }

    environment {
        SNYK_TOKEN = credentials('snyk-api-token')  // Reference the Jenkins secret
    }

    stages {
        stage('Install Podman Docker Package') {
            steps {
                container('podman') {
                    sh 'yum install -y podman-docker'
                    sh 'ls -l /var/run/docker.sock'

                }
            }
        }

        // stage('Start Podman API') {
        //     steps {
        //         container('podman') {
        //             script {
        //                 sh 'podman system service --time=0 unix:///run/podman/podman.sock &'
        //                 env.DOCKER_HOST = 'unix:///run/podman/podman.sock'
        //             }
        //         }
        //     }
        // }

        stage('Check Podman') {
            steps {
                container('podman') {
                    sh 'podman --version'
                }
            }
        }

        stage('Build') {
            steps {
                sh 'chmod +x ./gradlew'
                sh './gradlew clean build --info'
                sh 'ls -R lib/build/libs'
            }
        }

        stage('Podman Build') {
            steps {
                container('podman') {
                    sh 'podman build -t daundkarash/java-application_old_local .'
                    sh 'podman save -o /var/lib/containers/java-application_old_local.tar daundkarash/java-application_old_local'
                }
            }
        }

        stage('Load Image') {
            steps {
                container('podman') {
                    sh 'podman load -i /var/lib/containers/java-application_old_local.tar'
                }
            }
        }

        stage('Verify Image') {
            steps {
                container('podman') {
                    sh 'podman images'
                }
            }
        }

        stage('Snyk Container Scan') {
            steps {
                container('snyk') {
                    script {
                        env.DOCKER_HOST = 'unix:///run/podman/podman.sock'
                    }
                    sh 'snyk auth $SNYK_TOKEN'  // Authenticate with Snyk 
                    sh 'snyk container test localhost/daundkarash/java-application_old_local:latest --file=Dockerfile --debug'
                }
            }
        }
    }
}
