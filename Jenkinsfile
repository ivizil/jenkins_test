#!groovy

pipeline {
    agent any
    environment {
        
        BASE_IMAGE_TARGETS="readme.md/|Jenkinsfile"
        
    }
    stages {
        // setting env here because pipeline level env cannot be overridden
        stage("Checkout") {
            steps {
                // pulling master branch to make diff later
                script {
                    checkout([$class: 'GitSCM',
                        branches: scm.branches,
                        doGenerateSubmoduleConfigurations: false,
                        extensions: [],
                        submoduleCfg: [],
                        userRemoteConfigs: [
                            [
                                //credentialsId: 'github_bot_creds',
                                url: 'https://github.com/ivizil/jenkins_test'
                            ]
                        ]
                    ])
                }
                // getting files changed in current branch for base and tests images
                script {
                        if (env.BRANCH_NAME == "master") {
                                echo "Master branch" //env.BASE_IMAGE_TAG = env.GIT_COMMIT
                                env.BASE_IMAGE_AFFECTED = sh(script: "git diff --name-only origin/master^ | grep -E '${BASE_IMAGE_TARGETS}' | wc -l", returnStdout: true)
                                //echo "BIA - ${env.BASE_IMAGE_AFFECTED}"
                            }
                        else{
                                echo "Non master branch" //env.BASE_IMAGE_TAG = env.GIT_COMMIT
                                env.BASE_IMAGE_AFFECTED = sh(script: "git diff --name-only origin/master | grep -E '${BASE_IMAGE_TARGETS}' | wc -l", returnStdout: true)      
                }
            }
        }
        stage("Base Image") {
            when {
                    expression { env.BASE_IMAGE_AFFECTED.toInteger() > 0 }
            }
            stages {
                stage("Build Base Image") {
                    steps {
                        script{
                            echo "Build"
                            
                        }
                        script {
                            if (env.BRANCH_NAME != "master") {
                                echo "Non master" //env.BASE_IMAGE_TAG = env.GIT_COMMIT
                            }
                        }
                    }
                }
                stage("Push Base Image"){
                    when {
                        branch 'master'
                    }
                    failFast true
                    parallel {
                        stage("Base By Commit Tag") {
                           steps {
                                script {
                                    echo "push image " // base_image.push()
                                }
                           }
                        }
                        stage("Base By Latest") {
                            when { branch 'master' }
                            steps {
                                script {
                                    echo "push image last" //base_image.push('latest')
                                }
                            }
                        }
                    }
                }
            }
        }
      } 
    }
    post {
        // always {
                //sh("docker rmi ${BASE_IMAGE}:latest ${BASE_IMAGE_REF} || true")
        // }
        success{
        echo "Success"
        }
        unsuccessful {
        echo "Unsuccess"
        }
        fixed {
        echo "Fixed"
        }
    }
}
