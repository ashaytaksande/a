pipeline {
    agent 'any'
    environment {
        destination = 'ubuntu@ec2-18-206-252-248.compute-1.amazonaws.com'
        version = "${env.BUILD_ID}-${env.GIT_COMMIT.substring(0, 4)}"
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
                sh 'echo "website-${version}.tar" >> .dockerignore'
                sh '''
                docker build -t ashayalmighty/website:${version} .
                docker save -o website-${version}.tar ashayalmighty/website:${version}
                rsync -azPpr -e ssh website-${version}.tar ${destination}:/home/ubuntu/workspace/test
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
                agent {
                    label 'ProdNode'
                }
                steps {
                    //sh 'echo both jobs completed successfully'
                    sh '''
                    pwd
                    docker load -i website-${version}.tar
                    docker run -d -p 82:80 --name website ashayalmighty/website:${version}
                    '''
                }
            }
    }
}
