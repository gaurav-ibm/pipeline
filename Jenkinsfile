def repoList = 'undefined'
def scmURL = 'https://github.com/gaurav-ibm/'
def scmBranch = 'main'
def scmCredentialsId = 'github_credentials'
def buildStages = [:]

pipeline {
    agent any
    tools {
        maven 'Maven'
        gradle 'Gradle'
    }
    parameters {
        string(name: 'APP_NAME', defaultValue: 'ODS (Operational Data Store)', description: 'Name of application', trim: true)
        //string(name: 'APP_TYPE', defaultValue: 'MS', description: 'type of application, MS stands for microservices', trim: true)
        string(name: 'REPO_LIST', defaultValue: 'repoList.csv', description: 'Name of CSV file containing the list of modules comprising this application, one per line', trim: true)
    }
    stages {
        stage ("read CSV") {
            steps {
                script {
                    echo "reading CSV file"
                    repoList = readTrusted(params.REPO_LIST).readLines()
                    buildStages = prepareBuildStages(repoList)
                }
            }
        }

        stage ("build microservices") {
            steps {
                script {
                    parallel buildStages 
                }
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

// Create List of build stages to suit
def prepareBuildStages(List repoList) {
    def buildParallelMap = [:]
    for (line in repoList ) {
        def elements = line.split(',')
        def name = "${elements[0]}"
        def buildTool = elements[1]
        buildParallelMap.put(name, prepareOneBuildStage(name, buildTool))
    }
    return buildParallelMap
}

def prepareOneBuildStage(String name, String buildTool) {
  return {
    stage("Build stage: ${name}") {
        catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
            checkout([
                $class: 'GitSCM', 
                branches: [[name: "*/main"]], 
                doGenerateSubmoduleConfigurations: false, 
                extensions: [[
                    $class: 'RelativeTargetDirectory', 
                    relativeTargetDir: "microservices/${name}"
                ]], 
                submoduleCfg: [], 
                userRemoteConfigs: [[
                    credentialsId: "github_credentials", 
                    url: "https://github.com/gaurav-ibm/${name}.git"
                    ]]
            ])
            dir("microservices/${name}") {
                steps {
                   sh 'mvn -B -DskipTests clean package' 
                }
            }
            println("Building ${name} using ${buildTool}")
        }
    }
  }
}
