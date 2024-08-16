// Define the container list outside of the podTemplate block
containerList = [
    containerTemplate(
        name: 'podman',
        image: 'quay.io/podman/stable:latest',
        command: 'sleep',
        args: 'infinity',
        securityContext: [privileged: true],
        volumeMounts: [
            mountPath: '/var/lib/containers',
            name: 'podman-graph-storage'
        ]
    )
]

podTemplate(
    cloud: 'kubernetes-1',
    containers: containerList,
    volumes: [
        emptyDirVolume(mountPath: '/var/lib/containers', name: 'podman-graph-storage')
    ],
    podRetention: never(),
    showRawYaml: true
) {
    node(POD_LABEL) {
        stage('Check Podman') {
            container('podman') {
                sh 'podman --version'
            }
        }

        stage('Build') {
                // Run the Gradle build command
                sh './gradlew clean build --stacktrace -i'
        }

        stage('Podman Build') {
            container('podman') {
                // Build the container image using Podman
                sh 'podman build -t daundkarash/java-application_second .'
            }
        }

        stage('Push image to GitLab') {
            container('podman') {
                script {
                    withCredentials([usernamePassword(credentialsId: 'gitlab-registry', usernameVariable: 'GITLAB_USER', passwordVariable: 'GITLAB_TOKEN')]) {
                        // Log in to GitLab Container Registry
                        sh 'podman login registry.gitlab.com -u ${GITLAB_USER} -p ${GITLAB_TOKEN}'
                        
                        // Tag the image for GitLab Container Registry
                        sh 'podman tag daundkarash/java-application_second registry.gitlab.com/test8011231/jenkins-image-push/java-application_second:latest'
                        
                        // Push the image to GitLab Container Registry
                        sh 'podman push registry.gitlab.com/test8011231/jenkins-image-push/java-application_second:latest'
                    }
                }
            }
        }
    }
}
