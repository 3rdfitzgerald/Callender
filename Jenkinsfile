pipeline {
    agent any
    
    tools {nodejs "node"}
        stages {
            stage ('Cloning Git'){ 
                steps { 
                    git 'https://github.com/3rdfitzgerald/Callender'
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
