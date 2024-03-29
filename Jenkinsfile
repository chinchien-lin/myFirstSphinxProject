pipeline {
    agent {
        dockerfile {
          filename "Dockerfile"
          args "-u root" //needed to get around permission issues
        }
    }
    environment {
        SPHINX_DIR  = '.'
        BUILD_DIR   = './docs/build'
        SOURCE_DIR  = './docs/source'
        DEPLOY_HOST = 'deployer@www.example.com:/path/to/docs/'
    }
    stages {
        stage('Install Dependencies') {
            steps {
                // virtualenv may not be necessary with root,
                // but I still think it's a good idea.
                sh '''
                   virtualenv pyenv
                   . pyenv/bin/activate
                   pip install -r ${SPHINX_DIR}/requirements.txt
                '''
            }
        }
        stage('Build') {
            steps {
                // clear out old files
                sh 'rm -rf ${BUILD_DIR}'
                sh 'rm -f ${SPHINX_DIR}/sphinx-build.log'

                sh '''
                   ${WORKSPACE}/pyenv/bin/sphinx-build \
                   -q -w ${SPHINX_DIR}/sphinx-build.log \
                   -b html \
                   -d ${BUILD_DIR}/doctrees ${SOURCE_DIR} ${BUILD_DIR}
                '''
            }
            post {
                failure {
                    sh 'cat ${SPHINX_DIR}/sphinx-build.log'
                }
            }
        }
    }
}
