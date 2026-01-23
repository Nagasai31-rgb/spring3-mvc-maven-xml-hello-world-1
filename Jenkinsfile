pipeline {
    agent any

    tools {
        maven "MVN_HOME"
    }

    environment {
        NEXUS_VERSION       = "nexus3"
        NEXUS_PROTOCOL      = "http"
        NEXUS_URL           = "3.133.145.136:8081"
        NEXUS_REPOSITORY    = "devops"
        NEXUS_CREDENTIAL_ID = "Nexus_server"

        GROUP_ID    = "com.ncodeit"
        ARTIFACT_ID = "ncodeit-hello-world"
        VERSION     = "3.0"
        PACKAGING   = "war"
    }

    stages {

        stage("Clone Code") {
            steps {
                git 'https://github.com/Nagasai31-rgb/spring3-mvc-maven-xml-hello-world-1.git'
            }
        }

        stage("Maven Build") {
            steps {
                sh 'mvn -Dmaven.test.failure.ignore=true clean install'
            }
        }

        stage("Publish to Nexus") {
            steps {
                script {
                    def artifactPath = "target/${ARTIFACT_ID}-${VERSION}.${PACKAGING}"

                    if (!fileExists(artifactPath)) {
                        error "Artifact not found: ${artifactPath}"
                    }

                    echo "Uploading ${artifactPath} to Nexus"

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

        stage('Verify Nexus Credentials') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'NEXUS_CREDENTIAL_ID',
                    usernameVariable: 'NEXUS_USER',
                    passwordVariable: 'NEXUS_PASS'
                )]) {
                    sh 'echo "Nexus credentials loaded successfully"'
                }
            }
        }
    }
}
