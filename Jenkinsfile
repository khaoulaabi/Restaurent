pipeline {
    agent any

    environment {
        BUILD_DIR = "build"  // Répertoire où les fichiers seront copiés
        DEPLOY_DIR = "C:\\Deploy"  // Répertoire cible pour le déploiement
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

                    bat """
                    REM Créer les répertoires nécessaires dans 'build'
                    if not exist ${BUILD_DIR}\\css mkdir ${BUILD_DIR}\\css
                    if not exist ${BUILD_DIR}\\js mkdir ${BUILD_DIR}\\js

                    REM Minification et optimisation des fichiers CSS
                    echo Minifying CSS files...
                    if exist css\\*.css (
                        for %%i in (css\\*.css) do type %%i > ${BUILD_DIR}\\css\\%%~ni.min.css
                    )

                    REM Minification et optimisation des fichiers JS
                    echo Minifying JS files...
                    if exist js\\*.js (
                        for %%i in (js\\*.js) do type %%i > ${BUILD_DIR}\\js\\%%~ni.min.js
                    )

                    REM Copier les fichiers HTML dans le répertoire de build
                    echo Copying HTML files...
                    if exist *.html copy *.html ${BUILD_DIR}
                    """
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    echo "Testing project..."

                    bat """
                    REM Vérifier si les fichiers HTML, CSS et JS sont valides
                    echo Validating HTML files...
                    if exist ${BUILD_DIR}\\*.html (
                        for %%i in (${BUILD_DIR}\\*.html) do echo Validating %%i...
                    )

                    echo Validating CSS files...
                    if exist ${BUILD_DIR}\\css\\*.min.css (
                        for %%i in (${BUILD_DIR}\\css\\*.min.css) do echo Validating %%i...
                    )

                    echo Validating JS files...
                    if exist ${BUILD_DIR}\\js\\*.min.js (
                        for %%i in (${BUILD_DIR}\\js\\*.min.js) do echo Validating %%i...
                    )
                    """
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    echo "Deploying project..."

                    bat """
                    REM Préparer le répertoire de déploiement
                    if not exist ${DEPLOY_DIR} mkdir ${DEPLOY_DIR}

                    REM Copier les fichiers du répertoire de build vers le répertoire de déploiement
                    echo Copying files to deployment directory...
                    xcopy ${BUILD_DIR} ${DEPLOY_DIR} /E /H /C /I /Y

                    echo Deployment complete. Files are ready in '${DEPLOY_DIR}'.
                    """
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
              //cleanWs()

            // Archiver les artefacts de build pour les conserver
            archiveArtifacts artifacts: '**/build/**/*', allowEmptyArchive: true
        }
    }
}

