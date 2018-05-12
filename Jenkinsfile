#!/usr/bin/env groovy

pipeline {
    agent any

//    parameters {
//        string(name: 'payload', defaultValue: '', description: 'GitHub Webhook payload')
//    }

    stages {
        stage('Check') {
            steps {
                script {
                    echo "payload: ${params.payload}"

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
                    image 'python:2.7.14'
                    reuseNode true
                }
            }
            steps {
                sh 'python -m compileall src'
            }
        }

        stage('Test') {
            steps {
                echo 'run Test'
            }
        }

        stage('Archive') {
            steps {
                sh 'mkdir $MY_GIT_TAG'
                sh '''
                for src in $(find src -type f -path '*.pyc'); do
                    dest=$MY_GIT_TAG/$src
                    test -d $(dirname $dest) || mkdir -p $(dirname $dest)
                    cp $src $dest
                done
                '''
                sh 'ls -lR'
                zip zipFile: "${env.MY_GIT_TAG}.zip", archive: true, dir: env.MY_GIT_TAG
            }
        }

        stage('Upload') {
            steps {
                echo 'Upload'
            }
        }

    } // stages

    post {
        cleanup {
            sh 'git reset --hard $GIT_COMMIT'
            sh 'git clean -fdx'
        }
    }
}
