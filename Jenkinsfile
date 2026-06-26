def registry = 'https://trialmo6ogm.jfrog.io/'

pipeline {
    agent any

    environment {
        PATH = "/opt/maven/bin:${env.PATH}"
    }

    stages {

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('SonarQube Analysis') {
            environment {
                scannerHome = tool 'sonarqube-scanner'
            }

            steps {
                withSonarQubeEnv('sonarqube-connect') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }

        stage('Jar Publish') {
            steps {
                script {
                    echo '<---------------- Jar Publish Started ---------------->'

                    def server = Artifactory.newServer(
                        url: registry + "/artifactory",
                        credentialsId: "artifactory-cred"
                    )

                    def properties = "buildId=${env.BUILD_ID};commitId=${GIT_COMMIT}"

                    def uploadSpec = """
                    {
                        "files": [
                            {
                                "pattern": "jarstaging/(*)",
                                "target": "durgaraj-libs-snapshot-local/{1}",
                                "flat": "false",
                                "props": "${properties}",
                                "exclusions": ["*.sha1", "*.md5"]
                            }
                        ]
                    }
                    """

                    def buildInfo = server.upload(uploadSpec)
                    buildInfo.env.collect()
                    server.publishBuildInfo(buildInfo)

                    echo '<---------------- Jar Publish Ended ---------------->'
                }
            }
        }
    }
}
