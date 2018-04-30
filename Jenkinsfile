#!/usr/bin/env groovy

pipeline {
    agent any
    stages {
        stage('Check') {
            steps {
                script {
                    gitHeadCommit = sh(script: 'git rev-parse HEAD', returnStdout: true).trim()
                    echo "gitHeadCommit: ${gitHeadCommit}"

                    def gitTag = sh(script: 'git tag --points-at HEAD | tail -1', returnStdout: true).trim()
                    echo "gitTag: ${gitTag}"

                    def gitMasterCommit = sh(script: 'git rev-parse origin/master', returnStdout: true).trim()
                    echo "gitMasterCommit: ${gitMasterCommit}"

                    def gitDevelopCommit = sh(script: 'git rev-parse origin/develop', returnStdout: true).trim()
                    echo "gitDevelopCommit: ${gitDevelopCommit}"

                    def gitBranchIsMaster = gitHeadCommit == gitMasterCommit
                    echo "gitBranchIsMaster: ${gitBranchIsMaster}"

                    def gitBranchIsDevelop = gitHeadCommit == gitDevelopCommit
                    echo "gitBranchIsDevelop: ${gitBranchIsDevelop}"

                    sh 'env | sort'
                }
            }
        } // stage Check

        stage('Test') {
            steps {
                echo 'Hello world'
            }
        } // stage Test
    }
}
