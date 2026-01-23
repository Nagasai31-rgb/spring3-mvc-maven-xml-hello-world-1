pipeline {
    agent any

    tools {
        maven "MVN_HOME"
    }

    environment {
        // Nexus details
        NEXUS_VERSION       = "nexus3"
        NEXUS_PROTOCOL      = "http"
        NEXUS_URL           = "http://18.206.185.79:8081"
        NEXUS_REPOSITORY    = "devops"
        NEXUS_CREDENTIAL_ID = "Nexus_server"

        // Maven artifact details
        GROUP_ID    = "com.ncodeit"
        ARTIFACT_ID = "ncodeit-hello-world"
        VERSION     = "3.0"
        PACKAGING   = "war"
    }

    stages {

        stage("Checkout Source") {
            steps {
                git 'https://github.com/Nagasai31-rgb/spring3-mvc-maven-xml-hello-world-1.git'
            }
        }

        stage("Build with Maven") {
            steps {
                sh 'mvn -Dmaven.test.failure.ignore=true clean install'
            }
        }

        stage("Publish Artifact to Nexus") {
            steps {
                script {
                    def artifactPath = "target/${ARTIFACT_ID}-${VERSION}.${PACKAGING}"

                    if (!fileExists(artifactPath)) {
                        error "❌ Artifact not found: ${artifactPath}"
                    }

                    echo "🚀 Uploading ${artifactPath} to Nexus repository '${NEXUS_REPOSITORY}'"

                    nexusArtifactUploader(
                        nexusVersion: NEXUS_VERSION,
                        protocol: NEXUS_PROTOCOL,
                        nexusUrl: NEXUS_URL,
                        repository: NEXUS_REPOSITORY,
                        credentialsId: NEXUS_CREDENTIAL_ID,
                        groupId: GROUP_ID,
                        version: VERSION,
                        artifacts: [
                            [
                                artifactId: ARTIFACT_ID,
                                classifier: '',
                                file: artifactPath,
                                type: PACKAGING
                            ],
                            [
                                artifactId: ARTIFACT_ID,
                                classifier: '',
                                file: "pom.xml",
                                type: "pom"
                            ]
                        ]
                    )
                }
            }
        }

        stage("Verify Nexus Access (Optional)") {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: NEXUS_CREDENTIAL_ID,
                    usernameVariable: 'NEXUS_USER',
                    passwordVariable: 'NEXUS_PASS'
                )]) {
                    sh 'echo "✅ Nexus credentials loaded for user: $NEXUS_USER"'
                }
            }
        }
    }

    post {
        success {
            echo "🎉 Build and Nexus deployment completed successfully!"
        }
        failure {
            echo "❌ Build or Nexus deployment failed. Check logs above."
        }
    }
}
