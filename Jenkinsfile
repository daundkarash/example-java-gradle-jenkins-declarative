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
                // env:
                // - name: HTTP_PROXY
                //   value: "http://23.38.59.137:443"
                // - name: HTTPS_PROXY
                //   value: "http://23.38.59.137:443"
                // - name: NO_PROXY
                //   value: "localhost,127.0.0.1"
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
        stage('Check Podman') {
            steps {
                container('podman') {
                    sh 'podman --version'
                }
            }
        }

        // stage('Build') {
        //     steps {
        //         sh 'chmod +x ./gradlew'
        //         sh './gradlew clean build --info'
        //         sh 'ls -R lib/build/libs'
        //     }
        // }

        stage('Podman Build') {
            steps {
                container('podman') {
                    sh 'podman pull registry.access.redhat.com/ubi7/ubi:7.6'
                    sh 'podman images'
                    sh 'podman save 247ee58855fd -o /var/lib/containers/ubi76.tar'
                    sh 'ls -l /var/lib/containers'
                    // sh 'podman build -t daundkarash/java-application_old_local .'
                    // sh 'podman save -o /var/lib/containers/java-application_old_local.tar daundkarash/java-application_old_local'
                }
            }
        }

        // stage('Load Image') {
        //     steps {
        //         container('podman') {
        //             sh 'podman load -i /var/lib/containers/java-application_old_local.tar'
        //         }
        //     }
        // }

        // stage('Verify Image') {
        //     steps {
        //         container('podman') {
        //             sh 'podman images | grep daundkarash/java-application_old_local'
        //         }
        //     }
        // }

        // stage('Verify Network Access') {
        //     steps {
        //         container('snyk') {
        //             sh 'ls -l /var/lib/containers/'  // Test network access
        //         }
        //     }
        // }

        stage('Snyk Container Scan') {
            steps {
                container('snyk') {
                    sh 'whoami'
                    sh 'id'
                    sh 'snyk auth $SNYK_TOKEN'  // Authenticate with Snyk 
                    sh 'snyk container test /var/lib/containers/ubi76.tar'
                    // sh 'snyk container test localhost/daundkarash/java-application_old_local:latest --file=Dockerfile --debug'
                    // sh 'snyk container test /var/lib/containers/java-application_old_local.tar --debug'  // Scan using image tag
                }
            }
        }
    }
}
