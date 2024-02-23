pipeline {
    agent 'any'
    environment {
        destination = 'ubuntu@ec2-54-86-210-149.compute-1.amazonaws.com'
        version = "${env.BUILD_ID}-${env.GIT_COMMIT.substring(0, 5)}"
        branchName = "${env.GIT_BRANCH.split('/').size() == 1 ? env.GIT_BRANCH.split('/')[-1] : env.GIT_BRANCH.split('/')[1..-1].join('/')}"
    }
    stages {
        stage('If commit is made to develop branch, just build the product, do not publish."') {
            agent {
                label 'build_image'
            }
            when {
                expression {
                    branchName == 'develop'
                }
            }
            steps {
                sh '''
                docker build -t ashayalmighty/website:${version} .
                docker save -o website-${version}.tar ashayalmighty/website:${version}
                '''
            }
        }
            stage('If commit is made to master branch, build and send image to production server') {
            agent {
                label 'build_image'
            }
            when {
                expression {
                    branchName == 'master'
                }
            }
            steps {
                sh '''
                docker build -t ashayalmighty/website:${version} .
                docker save -o website-${version}.tar ashayalmighty/website:${version}
                rsync -azPpr -e ssh website-${version}.tar ${destination}:/home/ubuntu/
                '''
            }
            }
            stage('publish website on port 82') {
                agent {
                    label 'publish'
                }
                steps {
                    sh '''
                    cd /home/ubuntu/
                    docker load -i website-${version}.tar
                    docker rm -f website
                    docker run -d -p 82:80 --name website ashayalmighty/website:${version}
                    '''
                }
            }
    }
}
