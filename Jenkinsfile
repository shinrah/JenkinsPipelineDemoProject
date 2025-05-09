pipeline {
    agent any

    environment {
        giurl = 'https://github.com/shinrah/JenkinsPipelineDemoProject.git'
        gitBranch = "${params.GIT_Branch_Tag}"
        formattedGitBranch = "${params.GIT_Branch_Tag}" // Adjust if formatting is needed
    }

    parameters {
        choice choices: ['Deploy', 'stop', 'start', 'restart'], name: 'mode', description: 'Choose mode for deployment or starting the application server'
        choice choices: ['DEV', 'master', 'UAT'], name: 'host', description: 'Choose environment to deploy master'
        choice choices: ['DEV', 'master', 'feature-branch', 'UAT'], name: 'GIT_Branch_Tag', description: 'Select Git branch to deploy'
    }

    stages {
        stage('Print environment and mode') {
            steps {
                script {
                    env.Host = sh(returnStdout: true, script: "echo ${params.host}").trim()
                    env.Mode = sh(returnStdout: true, script: "echo ${params.mode}").trim()
                    env.Deployment_Method = sh(returnStdout: true, script: "echo ${params.mode}").trim()
                    env.GitBranch = params.GIT_Branch_Tag
                }
            }
        }

        stage('Checkout Git Branch') {
            steps {
                git branch: "${env.GitBranch}", url: "${env.giurl}"
            }
        }

        stage("Build Artifacts") {
            steps {
                script {
                    if (params.mode == "Deploy") {
                        currentBuild.displayName = "${params.host}_${params.mode}_${BUILD_NUMBER}"
                        echo "Ready to build for ${params.host} from branch ${params.GIT_Branch_Tag}"
                    }
                }
            }
        }

        stage('Clean up') {
            steps {
                script {
                    cleanWs()
                    echo "Formatted GIT branch tag is ${formattedGitBranch}"
                }
            }
        }

        stage('Preparation') {
            steps {
                script {
                    echo 'Checkout code from source code repository'
                    if ("${gitBranch}" != "") {
                        checkout([
                            $class: 'GitSCM',
                            branches: [[name: "${formattedGitBranch}"]],
                            doGenerateSubmoduleConfigurations: false,
                            extensions: [],
                            submoduleCfg: [],
                            userRemoteConfigs: [[url: "${giurl}"]]
                        ])
                    }
                }

                stage('building Scope JAVA'){
                    echo 'Exucuting scope JAVA Build'
                    artifactsbuildMaven.run pom: 'pom.xml', goals: 'clean install'
                }
            }
        }
    }
}
