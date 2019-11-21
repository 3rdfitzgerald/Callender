pipeline {
    agent any
    
    tools {nodejs "node"}
        stages {
            stage ('Cloning Git'){ 
                steps { 
                    git 'https://github.com/gustavoapolinario/nodo-todo-frontend'
                }
            }
            
            stage ('Install dependencies') {
                steps {
                        sh 'npm install'
                }
            }
            
            stage('Test') {
                    steps {
                            sh 'npm test'
                    }
            }
            
        }
        
}
