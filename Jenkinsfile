pipeline {
    agent any
    parameters { 
       choice(name: 'REPO_LIST', choices: ['box1', 'box2'], description: 'list of names of repositories in github to be build and zipped for veracode scan')
    }
    tools {
        maven "Maven"
        gradle "Gradle"
    }
    stages {
        stage ("build") {
            echo "inside build stage"
            def repoList = param.REPO_LIST.split('\n');
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