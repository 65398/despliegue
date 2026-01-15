pipeline {
    agent any

    tools {
        nodejs "Node25"
        dockerTool "Dockertool"
    }

    stages {
        stage('Instalar dependencias') {
            steps {
                sh 'npm install'
            }
        }

        stage('Ejecutar tests') {
            steps {
                // Ejecuta Jest en modo CI
                sh 'npm test -- --ci --runInBand'
            }
        }

        stage('Construir Imagen Docker') {
            when {
                expression { currentBuild.result == null || currentBuild.result == 'SUCCESS' }
            }
            steps {
                script {
                    // Construye la imagen usando el plugin Docker Pipeline
                    docker.build("hola-mundo-node:latest")
                }
            }
        }

        stage('Ejecutar Contenedor Node.js') {
            when {
                expression { currentBuild.result == null || currentBuild.result == 'SUCCESS' }
            }
            steps {
                script {
                    // Detiene y elimina contenedor previo si existe
                    try {
                        sh 'docker stop hola-mundo-node || true'
                        sh 'docker rm hola-mundo-node || true'
                    } catch (err) {
                        echo "No había contenedor previo"
                    }

                    // Ejecuta el contenedor con el puerto 3000 expuesto
                    docker.image("hola-mundo-node:latest").run("-p 3000:3000 --name hola-mundo-node")
                }
            }
        }
    }
}
