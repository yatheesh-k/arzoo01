pipeline {
    agent any
    environment {
        NEXUS_URL = 'http://13.235.19.107:8081/repository/application_react/'
        NEXUS_CREDENTIALS_ID = 'nexus' // ID of the stored credentials in Jenkins
    }
    stages {
        stage('Clone Repository') {
            steps {
                //git 'https://github.com/yatheesh-k/arzoo01.git'
                git branch: 'main', url: 'https://github.com/yatheesh-k/arzoo01.git'
            }
        }

        stage('npm install'){
            steps {
                 
                sh '''
                 ls -ltr
                 npm install
                 '''
             }
         }
        
   
        stage('npm build') {
            steps {
                sh 'npm run build'
            }
        }
        stage('Zip Dist Directory') {
            steps {
                sh '''
                zip -r dist-${BUILD_ID}.zip dist
                '''
            }
        }
        stage('SonarQube analysis') {
            environment {
              SCANNER_HOME = tool 'sonar-scanner'
            }
            steps {
            withSonarQubeEnv('sonar') {
                    sh "${SCANNER_HOME}/bin/sonar-scanner -Dsonar.projectKey=react-application -Dsonar.sources=src -Dsonar.host.url=http://13.232.115.201:9000/-Dsonar.login=sqp_3cf78ef11a74f81d85e989d7865ee0c76f468321"
                }
            }
        }
       stage('Upload Artifact') {
            steps {
               script {
                   nexusArtifactUploader(
                       nexusVersion: 'nexus3',
                       protocol: 'http',
                       nexusUrl: 'http://13.232.246.255:8081/',
                       version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
                       groupId: 'com.demo1.www',
                       repository: 'javaappl',
                       credentialsId: 'nexuslogin',
                       artifacts: [
                         [artifactId: 'javaappl',
                           classifier: '',
                           file: 'target/practise1.war',
                           type: 'war']
                       ]
                   )
               }
           }
   }

    post {
        always {
            // cleanup steps, if any
            sh 'echo "Always do cleanup actions here"'
        }
        success {
            sh 'echo "Pipeline succeeded"'
        }
        failure {
            sh 'echo "pipeline failed"'
        }
    }




