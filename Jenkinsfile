pipeline {
    agent any
    parameters { 
       choice(name: 'REPO_LIST', choices: ['box1', 'box2'], description: 'list of names of repositories in github to be build and zipped for veracode scan')
    tools {
        maven: "maven",
        gradle: "gradle"
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
}