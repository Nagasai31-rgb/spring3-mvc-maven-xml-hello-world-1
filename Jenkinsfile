pipeline {
    agent any

    tools {
        maven "MVN_HOME"
    }

    environment {
        NEXUS_VERSION    = "nexus3"
        NEXUS_PROTOCOL   = "http"
        NEXUS_URL        = "18.206.185.79:8081"
        NEXUS_REPOSITORY = "devops"

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

        stage("Publish Artifact to Nexus (NO AUTH)") {
            steps {
                script {
                    def artifactPath = "target/${ARTIFACT_ID}-${VERSION}.${PACKAGING}"

                    nexusArtifactUploader(
                        nexusVersion: NEXUS_VERSION,
                        protocol: NEXUS_PROTOCOL,
                        nexusUrl: NEXUS_URL,
                        repository: NEXUS_REPOSITORY,
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
    }
}
