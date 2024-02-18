pipeline {
    agent 'any'
    environment {
        version = "${env.BUILD_ID}-${env.GIT_COMMIT}"
        branchName = "${env.GIT_BRANCH.split('/').size() == 1 ? env.GIT_BRANCH.split('/')[-1] : env.GIT_BRANCH.split('/')[1..-1].join('/')}"
    }
    stages {
        stage('If commit is made to develop branch, just build the product, do not publish."') {
            agent {
                label 'TestNode'
            }
            when {
                expression {
                    branchName == 'develop'
                }
            }
            steps {
                sh '''
                docker build -t ashayalmighty/website:${version} .
                '''
            }
        }
            stage('If commit is made to master branch, build and publish website on port 82') {
            agent {
                label 'TestNode'
            }
            when {
                expression {
                    branchName == 'master'
                }
            }
            steps {
                echo 'hello'
            }
            }
            stage('message') {
                steps {
                    sh 'echo both jobs completed successfully'
                }
            }
    }
}
