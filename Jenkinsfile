def repoList = 'undefined'
def buildStages = [:]
def globalWS = ''

pipeline {
    agent any
    tools {
        maven 'Maven'
        gradle 'Gradle'
    }
    parameters {
        string(name: 'APP_NAME', defaultValue: 'ODS (Operational Data Store)', description: 'Name of application', trim: true)
        string(name: 'REPO_LIST', defaultValue: 'repoList.csv', description: 'Name of CSV file containing the list of modules comprising this application, one per line', trim: true)
    }
    stages {
        stage ("read CSV") {
            steps {
                script {
                    echo "reading CSV file"
                    repoList = readTrusted(params.REPO_LIST).readLines()
                    buildStages = prepareBuildStages(repoList)
                    echo "${WORKSPACE}"
                    globalWS = WORKSPACE
                    bat "mkdir resultingJars"
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
        def name = elements[0]
        def buildTool = elements[1]
        if (buildTool.contains('maven')) {
            buildParallelMap.put(name, prepareMavenBuildStage(name))
        }
        if (buildTool.contains('gradle')) {
            buildParallelMap.put(name, prepareGradleBuildStage(name))
        }
    }
    return buildParallelMap
}

def prepareMavenBuildStage(String name) {
  return {
    stage("Build : ${name}") {
        catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
            //checkoutCode(name)
            def jobFolder = JOB_NAME.replace("/", "_")
            def targetFolder = "${WORKSPACE}\\..\\..\\workspace\\${jobFolder}\\resultingJars"
            ws("microservices/${name}") {
                checkout([
                    $class: 'GitSCM', 
                    branches: [[name: "*/main"]], 
                    doGenerateSubmoduleConfigurations: false, 
                    extensions: [[
                        $class: 'RelativeTargetDirectory', 
                        relativeTargetDir: ""
                    ]], 
                    submoduleCfg: [], 
                    userRemoteConfigs: [[
                        credentialsId: "github_credentials", 
                        url: "https://github.com/gaurav-ibm/${name}.git"
                    ]]
                ])
                bat 'mvn -B -DskipTests clean package'
                bat "dir target\\*.jar"
                println("Inside maven build stage. WS: ${WORKSPACE}")
                bat "copy target\\*.jar ${targetFolder}"
                bat "dir ${targetFolder}"
            }
            println("Built ${name} using maven}")
        }
    }
  }
}

def prepareGradleBuildStage(String name) {
  return {
    stage("Build : ${name}") {
        catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
            //checkoutCode(name)
            def jobFolder = JOB_NAME.replace("/", "_")
            def targetFolder = "${WORKSPACE}\\..\\..\\workspace\\${jobFolder}\\resultingJars"
            ws("microservices/${name}") {
                checkout([
                    $class: 'GitSCM', 
                    branches: [[name: "*/main"]], 
                    doGenerateSubmoduleConfigurations: false, 
                    extensions: [[
                        $class: 'RelativeTargetDirectory', 
                        relativeTargetDir: ""
                    ]], 
                    submoduleCfg: [], 
                    userRemoteConfigs: [[
                        credentialsId: "github_credentials", 
                        url: "https://github.com/gaurav-ibm/${name}.git"
                    ]]
                ])
                //sh "chmod +x gradlew"
                bat "gradle --no-daemon clean build -x test"
                bat "dir build\\libs\\*.jar"
                println("Inside gradle build stage. WS: ${WORKSPACE}")
                bat "copy build\\libs\\*.jar ${targetFolder}"
                bat "dir ${targetFolder}"
            }
            println("Built ${name} using gradle")
        }
    }
  }
}

def checkoutCode(String microserviceName) {
    checkout([
        $class: 'GitSCM', 
        branches: [[name: "*/main"]], 
        doGenerateSubmoduleConfigurations: false, 
        extensions: [[
            $class: 'RelativeTargetDirectory', 
            relativeTargetDir: "microservices/${microserviceName}"
        ]], 
        submoduleCfg: [], 
        userRemoteConfigs: [[
            credentialsId: "github_credentials", 
            url: "https://github.com/gaurav-ibm/${microserviceName}.git"
        ]]
    ])
}