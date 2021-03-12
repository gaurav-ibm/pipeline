def repoList = 'undefined'
def baseGitURL = 'https://github.com/gaurav-ibm/'
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
        stage ("scan CSV") {
            steps{
                echo "inside reading CSV stage"
                script {
                    repoList = readTrusted(params.REPO_LIST).readLines()
                }
                echo "${repoList}"
            }
        }
        stage ("bundle deployable jars") {
            steps {
                buildAllServices(repoList)
            }
        }
        stage ("scan") {
            steps {
                echo "inside scan stage"
            }
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

def buildAllServices(repoList) {
    script {
        for(int i=0; i < repoList.size(); i++) {
            stage("Building ${list[i]}"){
                echo "Element: $i"
            }
        }
    }
}