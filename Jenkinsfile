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

                    if (!gitBranchIsMaster) {
                        currentBuild.result = 'ABORTED'
                        error('not master branch')
                    }

                    if (!(gitTag ==~ /\Av3\.\d+\.\d+\.\d+\z/)) {
                        currentBuild.result = 'ABORTED'
                        error('not found release tag')
                    }

                    env.MY_GIT_TAG = gitTag
                }
            }
        } // stage Check

        stage('Build') {
            agent {
                docker {
                    image 'python:2-alpine'
                }
            }
            steps {
                sh 'python -m compileall src'
                sh 'find src -path "*.pyc"'
            }
        }

        stage('Test') {
            steps {
                echo 'run Test'
            }
        }

        stage('Archive') {
            steps {
                sh 'find src -type f -path "*.pyc"'
            }
        }

        stage('Upload') {
            steps {
                echo 'Upload'
            }
        }

    } // stages
}
