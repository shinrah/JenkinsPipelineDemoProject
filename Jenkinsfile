pipeline {
    agent any

    parameters {
        choice choices: ['Deploy', 'stop', 'start', 'restart'], description: 'Choose mode for deployment or starting the application server', name: 'mode'
        choice choices: ['DEV', 'master', 'UAT'], description: 'Choose environment to deploy master', name: 'host'

        // Instead of listbranches, we use a choice parameter for Git branch selection
        choice choices: ['DEV', 'master', 'feature-branch', 'UAT'], 
               description: 'Select Git branch to deploy', 
               name: 'GIT_Branch_Tag'
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
                git branch: "${env.GitBranch}", url: 'https://github.com/shinrah/JenkinsPipelineDemoProject.git'
            }
        }

        stage("Build Artifacts") {
            steps {
                script {
                    if (params.mode == "Deploy") {
                        currentBuild.displayName = "${params.host}_${params.mode}_${BUILD_NUMBER}"
                        def JDK = tool name: 'samsungJDK11'
                        def mvn = tool name: 'MVN111'
                        env.JAVA_HOME = "${JDK}"
                        env.MVN_BIN = "${mvn}/bin"
                        def environment = "${params.host}"
                        def giturl = 'https://github.com/shinrah/JenkinsPipelineDemoProject.git'
                        def gitBranch = "${params.GIT_Branch_Tag}"
                        def artifactServer = Artifactory.server('Devopsartifactoryserver')
                        def artifactbuildinfo = Artifactory.newBuildInfo()
                        def artifactbuildMaven = Artifactory.newMavenBuild()
                        def citoolpath = "${env.CITOOL_PATH}"
                        def devopssonarprops = readProperties file: "${citoolpath}/sonar.properties"
                        artifactbuildMaven.tool = 'MVN111'
                    }
                }
            }
        }

        stage('Clean up') {
            steps {
                script {
                    cleanWs()
                    def gitBranch = "${params.GIT_Branch_Tag}"
                    if (gitBranch != "") {
                        def formattedGitBranch = getGitBranchName(gitBranch)
                        echo "Formatted GIT branch tag is ${formattedGitBranch}"
                    }
                }
            }
        }

        stage('Prepration') {
            steps {
                echo 'Checkout Code from Source Code Repository'
                script {
                    if ("${gitBranch}" != "") {
                        def formattedGitBranch = getGitBranchName(gitBranch)
                        checkout([$class: 'GitSCM', branches: [[name: "${formattedGitBranch}"]], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], useRemoteConfig: true, url: "$giturl"])
                    }
                }
            }
        }

        stage('Building SIF JAVA') {
            steps {
                withCredentials([string(credentialsId: 'sonar-token', variable: 'sonartoken')]) {
                    echo 'Executing SIF JAVA Build'
                    script {
                        artifactbuildMaven.opts = "-Dsonar.host.url=\"${devopssonarprops.sonar_url}\""
                        artifactbuildMaven.run(
                            pomFile: 'Java/SproutService/pom.xml',
                            goals: 'clean install -Dmaven.test.failure.ignore=true sonar:sonar -Dsonar.projectKey=SIF -U -Dsonar.host.url=${SONAR_URL} -Dsonar.login=${sonartoken}',
                            buildInfo: artifactbuildinfo
                        )
                    }
                }
            }
        }
    }
}
