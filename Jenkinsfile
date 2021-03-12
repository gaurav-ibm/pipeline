def repoList = 'undefined'
pipeline {
    agent any
    tools {
        maven 'Maven'
        gradle 'Gradle'
    }
    parameters {
        string(name: 'APP_NAME', defaultValue: 'ODS (Operational Data Store)', description: 'Name of application', trim: true)
        string(name: 'APP_TYPE', defaultValue: 'MS', description: 'type of application, MS stands for microservices', trim: true)
        string(name: 'REPO_LIST', defaultValue: 'repoList.csv', description: 'Name of CSV file containing the list of modules comprising this application, one per line', trim: true)
    }
    stages {
        stage ("build") {
            echo "inside build stage"
            repoList = readTrusted(param.REPO_LIST)
            for (repo in repoList) {
                echo "working on repo: ${repo}"
            }
        }
        stage ("scan") {
            echo "inside scan stage"
        }
    }
    post {
        always {
            echo 'This will always run'
        }
        success {
            echo 'This will run only if successful'
        }
        failure {
            echo 'This will run only if failed'
        }
        unstable {
            echo 'This will run only if the run was marked as unstable'
        }
        changed {
            echo 'This will run only if the state of the Pipeline has changed'
            echo 'For example, if the Pipeline was previously failing but is now successful'
        }
    }
}