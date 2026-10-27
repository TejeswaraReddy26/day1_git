pipeline {
    agent any

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    stages {
        stage('Inspect Repository') {
            steps {
                echo 'Inspecting the checked-out repository'
                sh 'pwd'
                sh 'ls -la'
            }
        }

        stage('Validate Source') {
            steps {
                sh '''
                    set -eu
                    test -f Jenkinsfile
                    echo "Source validation passed"
                '''
            }
        }

        stage('Build Artifact') {
            steps {
                sh '''
                    set -eu
                    mkdir -p output
                    printf 'Jenkins CI build %s\n' "$BUILD_NUMBER" \
                      > output/build-info.txt

                    if [ -f README.md ]; then
                        cp README.md output/README.md
                    fi

                    echo "Build artifact created"
                    ls -l output
                '''
            }
        }
        
        stage('Test Artifact') {
            steps {
                sh '''
                    set -eu
                    test -s output/build-info.txt
                    grep -q "Jenkins CI build" output/build-info.txt
                    echo "Artifact test passed"
                '''
            }
        }
        stage('Show Build Details') {
             steps {
                 echo "Job name: ${env.JOB_NAME}"
                 echo "Build number: ${env.BUILD_NUMBER}"
                 echo "Build URL: ${env.BUILD_URL}"
            }
          }
        stage('Archive Artifact') {
            steps {
                archiveArtifacts(
                    artifacts: 'output/**',
                    fingerprint: true
                )
            }
        }
    }

    post {
        success {
            echo 'pipeline completed successfully'
        }

        failure {
            echo 'pipeline failed. Check Console Output.'
        }

        always {
            echo 'Pipeline execution has ended'
        }
    }
}
