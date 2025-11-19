pipeline {
    agent any

    stages {
        stage('SAST-Semgrep') {
            agent {
                docker {
                    image 'python:3.11-slim'
                    args '-u root'
                }
            }
            steps {
                script{
                    sh 'apt-get update && apt-get install -qq -y git'
                    sh 'git config --global --add safe.directory $WORKSPACE'
                    sh 'pip install -q semgrep'
                    
                    // Eliminar archivo anterior si existe
                    sh 'rm -f semgrep.json || true'
                    
                    try {
                        // CORREGIDO: Quitar --force, usar solo --json-output y --error
                        sh 'semgrep scan --json-output=semgrep.json --error .'
                    }
                    catch (err) {                                        
                        unstable(message: "Findings found")
                    }
                    
                    // Verificar que el archivo se creó
                    sh 'test -f semgrep.json && echo "✅ Archivo semgrep.json creado" || echo "❌ Archivo no existe, creando vacío..."'
                    sh 'test -f semgrep.json || echo "{}" > semgrep.json'
                    sh 'ls -la semgrep.json'
                }
                
                // Archivar directamente desde este stage
                archiveArtifacts artifacts: 'semgrep.json', fingerprint: true, allowEmptyArchive: true
            }
        }
        
        stage('Compilation') {
            agent {
                docker { image 'php:8.2-cli' }
            }
            steps {
                sh 'echo "Compilando..."'
            }
        }
        
        stage('Build') {
            agent {
                docker { image 'php:8.2-cli' }
            }
            steps {
                sh 'echo "docker build -t my-php-app ."'
            }
        }
        
        stage('Deploy') {
            agent {
                docker { image 'php:8.2-cli' }
            }
            steps {
                sh 'echo "docker run my-php-app ."'
            }
        }
    }
}
