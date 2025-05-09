pipeline {
    agent any

    parameters {
        choice(name: 'mode', choices: ['Deploy', 'stop', 'start', 'restart'], description: 'Choose mode for deployment or starting the application server')
        choice(name: 'host', choices: ['DEV', 'master', 'UAT'], description: 'Choose environment to deploy')
        choice(name: 'GIT_Branch_Tag', choices: ['DEV', 'master', 'feature-branch', 'UAT'], description: 'Select Git branch to deploy')
    }

    environment {
        GIT_REPO_URL = 'https://github.com/shinrah/JenkinsPipelineDemoProject.git'
    }

    stages {
        stage('Print environment and mode') {
            steps {
                script {
                    env.Host = params.host
                    env.Mode = params.mode
                    env.Deployment_Method = params.mode
                    env.GitBranch = params.GIT_Branch_Tag
                    echo "Mode: ${env.Mode}, Host: ${env.Host}, Git Branch: ${env.GitBranch}"
                }
            }
        }

        stage('Checkout Git Branch') {
            steps {
                git branch: "${params.GIT_Branch_Tag}", url: "${env.GIT_REPO_URL}"
            }
        }

        stage("Build Artifacts") {
            when {
                expression { return params.mode == "Deploy" }
            }
            steps {
                script {
                    currentBuild.displayName = "${params.host}_${params.mode}_${BUILD_NUMBER}"
                    echo "Ready to build for ${params.host} from branch ${params.GIT_Branch_Tag}"
                }
            }
        }

        stage('Clean up') {
            steps {
                script {
                    cleanWs()
                    echo "Formatted GIT branch tag is ${params.GIT_Branch_Tag}"
                }
            }
        }

        stage('Preparation') {
            steps {
                script {
                    echo 'Checkout code from source code repository'
                    checkout([
                        $class: 'GitSCM',
                        branches: [[name: "${params.GIT_Branch_Tag}"]],
                        doGenerateSubmoduleConfigurations: false,
                        extensions: [],
                        submoduleCfg: [],
                        userRemoteConfigs: [[url: "${env.GIT_REPO_URL}"]]
                    ])
                }
            }
        }

        stage('Building Scope JAVA') {
            steps {
                script {
                    def artifactsbuildMaven = Artifactory.newMavenBuild()
                    def artifactsbuildinfo = Artifactory.newBuildInfo()
                    artifactsbuildMaven.tool = 'MVN111'
                    artifactsbuildMaven.run pom: 'pom.xml', goals: 'clean install', buildInfo: artifactsbuildinfo
                }
            }
        }
    }
}
