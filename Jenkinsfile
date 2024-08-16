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
              volumes:
              - name: podman-graph-storage
                emptyDir: {}
            """
        }
    }

    stages {
        stage('Check Podman') {
            steps {
                container('podman') {
                    sh 'podman --version'
                }
            }
        } // Check Podman

        // stage('Build') {
        //     steps {
        //         // Run the Gradle build command in the Podman container
        //         sh './gradlew clean build --stacktrace -i'
        //     }
        // } // Build

        // stage('Podman Build') {
        //     steps {
        //         // Build the container image using Podman
        //         container('podman') {
        //             sh 'podman build -t daundkarash/java-application .'
        //         }
        //     }
        // } // Podman Build
        
        // stage('Push image to Hub') {
        //     steps {
        //         container('podman') {
        //             script {
        //                 withCredentials([string(credentialsId: 'dockerhub-pwd', variable: 'podmanpwd')]) {
        //                     sh 'podman login -u daundkarash -p ${podmanpwd}'
        //                 }
        //                 sh 'podman push daundkarash/java-application'
        //             }
        //         }
        //     }
        // } // Push image to Hub
    }
}
