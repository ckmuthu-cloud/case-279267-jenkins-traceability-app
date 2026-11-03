@Library('provenance-shared-lib') _

pipeline {
    agent any

    stages {
        stage('Build artifact') {
            steps {
                // Simulates building a container image / Lambda zip / binary.
                // In real life this would be `docker build` + `docker inspect --format '{{.Id}}'`,
                // or `aws lambda publish-version` + CodeSha256, or `sha256sum` on a build output.
                sh '''
                    echo "demo-app payload built at $(date -u +%Y-%m-%dT%H:%M:%SZ) from commit $GIT_COMMIT" > app.bin
                    sha256sum app.bin | awk '{print $1}' > computed_digest.txt
                '''
                script {
                    env.ARTIFACT_DIGEST = readFile('computed_digest.txt').trim()
                }
                echo "Built artifact with digest sha256:${env.ARTIFACT_DIGEST}"
            }
        }

        stage('Register provenance') {
            steps {
                script {
                    stampProvenance(
                        name:   'demo-app',
                        type:   'generic-checksum',
                        digest: "sha256:${env.ARTIFACT_DIGEST}"
                    )
                }
            }
        }
    }

    post {
        success {
            echo "Done. To trace this artifact later, look up MD5(\"sha256:${env.ARTIFACT_DIGEST}\") in the Jenkins fingerprint database."
        }
    }
}
