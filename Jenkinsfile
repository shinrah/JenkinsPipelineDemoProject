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
                    env.Deployment_Method = sh(returnStdout: true, script: "echo ${params.Deployment_Method}").trim()
                    env.GitBranch = params.GIT_Branch_Tag // Save the selected branch to an environment variable
                }
            }
        }

        // Add more stages as needed, using the selected Git branch
        stage('Checkout Git Branch') {
            steps {
                // Checkout the selected branch
                git branch: "${env.GitBranch}", url: 'https://github.com/shinrah/JenkinsPipelineDemoProject.git'
            }
        }
    }
}
