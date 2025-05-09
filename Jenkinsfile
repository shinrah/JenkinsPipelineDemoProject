pipeline {
    agent any

    parameters {
        choice(name: 'mode', choices: ['Deploy', 'stop', 'start', 'restart'], description: 'Choose mode...')
        choice(name: 'host', choices: ['DEV', 'master', 'UAT'], description: 'Choose environment...')
        choice(name: 'GIT_Branch_Tag', choices: ['DEV', 'master', 'feature-branch', 'UAT'], description: 'Select Git branch...')
    }

    stages {
        stage('Print environment and mode') {
            steps {
                script {
                    env.Host = params.host
                    env.Mode = params.mode
                    env.Deployment_Method = params.mode
                    env.GitBranch = params.GIT_Branch_Tag
                }
            }
        }

        stage('Checkout Git Branch') {
            steps {
                git branch: "${env.GitBranch}", url: 'https://github.com/shinrah/JenkinsPipelineDemoProject.git'
            }
        }

        stage("Build Artifacts") {
            when {
                expression { return params.mode == 'Deploy' }
            }
            steps {
                script {
                    // build logic...
                }
            }
        }

        stage('Clean up') {
            steps {
                script {
                    cleanWs()
                    def formattedGitBranch = getGitBranchName(params.GIT_Branch_Tag)
                    echo "Formatted GIT branch tag is ${formattedGitBranch}"
                }
            }
        }

        stage('Preparation') {
            steps {
                script {
                    def formattedGitBranch = getGitBranchName(params.GIT_Branch_Tag)
                    checkout([$class: 'GitSCM', branches: [[name: "${formattedGitBranch}"]], ...])
                }
            }
        }

        stage('Building SIF JAVA') {
            steps {
                withCredentials([string(credentialsId: 'sonar-token', variable: 'sonartoken')]) {
                    script {
                        // sonar + maven logic...
                    }
                }
            }
        }
    }
}
