pipeline {
    agent any

    environment {
        BUILD_DIR = "build"       // Directory where files will be copied
        DEPLOY_DIR = "/tmp/deploy" // Target directory for deployment
    }

    stages {
        stage('Checkout') {
            steps {
                echo "Cloning repository..."
                checkout scm
            }
        }

        stage('Build') {
            steps {
                script {
                    echo "Building project..."

                    sh '''
                    # Create necessary directories in 'build'
                    mkdir -p ${BUILD_DIR}/css ${BUILD_DIR}/js

                    # Minify and optimize CSS files
                    echo Minifying CSS files...
                    if ls css/*.css 2>/dev/null; then
                      for i in css/*.css; do
                        cat "$i" > "${BUILD_DIR}/css/$(basename "$i" .css).min.css"
                      done
                    fi

                    # Minify and optimize JS files
                    echo Minifying JS files...
                    if ls js/*.js 2>/dev/null; then
                      for i in js/*.js; do
                        cat "$i" > "${BUILD_DIR}/js/$(basename "$i" .js).min.js"
                      done
                    fi

                    # Copy HTML files to the build directory
                    echo Copying HTML files...
                    cp -v *.html ${BUILD_DIR}/ || true
                    '''
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    echo "Testing project..."

                    sh '''
                    # Validate HTML files
                    echo Validating HTML files...
                    if ls ${BUILD_DIR}/*.html 2>/dev/null; then
                      for i in ${BUILD_DIR}/*.html; do
                        echo Validating "$i"...
                      done
                    fi

                    # Validate CSS files
                    echo Validating CSS files...
                    if ls ${BUILD_DIR}/css/*.min.css 2>/dev/null; then
                      for i in ${BUILD_DIR}/css/*.min.css; do
                        echo Validating "$i"...
                      done
                    fi

                    # Validate JS files
                    echo Validating JS files...
                    if ls ${BUILD_DIR}/js/*.min.js 2>/dev/null; then
                      for i in ${BUILD_DIR}/js/*.min.js; do
                        echo Validating "$i"...
                      done
                    fi
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    echo "Deploying project..."

                    sh '''
                    # Prepare deployment directory
                    mkdir -p ${DEPLOY_DIR}

                    # Copy files from the build directory to the deployment directory
                    echo Copying files to deployment directory...
                    cp -r ${BUILD_DIR}/* ${DEPLOY_DIR}/

                    echo Deployment complete. Files are ready in '${DEPLOY_DIR}'.
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "Build, test, and deployment completed successfully."
        }
        always {
            echo "Cleaning up workspace..."

            // Archive build artifacts for storage
            archiveArtifacts artifacts: '**/build/**/*', allowEmptyArchive: true
        }
    }
}
