def repoList = 'undefined'
pipeline {
    agent any
    tools {
        maven 'Maven'
        gradle 'Gradle'
    }
    stages {
        stage ("setup parameters") {
            script {
                properties ([
                    parameters ([
                        string(
                            defaultValue: 'ODS (Operational Data Store)', 
                            name: 'APP_NAME', 
                            trim: true,
                            description: 'Name of Application'
                            ),
                        string(
                            defaultValue: 'MS', 
                            name: 'APP_TYPE', 
                            trim: true,
                            description: 'type of application, MS stands for microservices'
                            ),
                        string(
                            defaultValue: 'repoList.csv',
                            name: 'FILE_NAME',
                            trim: true,
                            description: 'Name of CSV file containing the list of modules comprising this application, one per line'
                        )
                    ])
                ])
            }
        }
        stage ("build") {
            echo "inside build stage"
            repoList = readFile(param.FILE_NAME)
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