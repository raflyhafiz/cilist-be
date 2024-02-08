pipeline {  
  agent any
  tools{
        jdk 'jdk17'
    }
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }
  options {
    buildDiscarder(logRotator(numToKeepStr: '5'))   
  }
  stages {
    stage('OWASP FS SCAN') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'DP-Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage('TRIVY FS SCAN') {
            steps {
                sh "trivy fs . > trivyfs.txt"
            }
        }
    stage('Build Image Backend') {
        steps {
            script{
                if (env.BRANCH_NAME == 'staging') {
                    dir('backend'){
                        sh 'docker buildx build -t raflyhafiz/cilist-be:0.0.$BUILD_NUMBER-staging .'
                    }
                }
                else if (env.BRANCH_NAME == 'master') {
                    dir('backend'){
                         sh 'docker buildx build -t raflyhafiz/cilist-be:0.0.$BUILD_NUMBER-master .' 
                    }
                }
                else {
                     sh 'echo Nothing to Build'
                }
            }
        }
    }
    stage('Push to Registry Backend') {
        steps {
            script {
             if (env.BRANCH_NAME == 'staging') {
            sh 'docker push raflyhafiz/cilist-be:0.0.$BUILD_NUMBER-staging'
                }
                else if (env.BRANCH_NAME == 'master') {
            sh 'docker push raflyhafiz/cilist-be:0.0.$BUILD_NUMBER-master' 
                }
                else {
                    sh 'echo Nothing to Push'
                }
        }
      }
    }
    stage("TRIVY"){
            steps{
                sh "trivy image raflyhafiz/cilist-be:0.0.$BUILD_NUMBER-staging > trivyimage.txt" 
            }
        }
    stage("Sonarqube Analysis "){
            steps{
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=becilist \
                    -Dsonar.projectKey=becilist '''
                }
            }
        }
    stage('Upload file'){
        steps{
            slackUploadFile filepath: 'trivyfs.txt,trivyimage.txt', initialComment: 'Here is your file'
        }
    }
        
    //  stage('Deploy to Kubernetes') {
    //     steps {
    //         script {
    //             if (env.BRANCH_NAME == 'staging') {
    //                 slackSend channel: '#jenkinsnotif',
    //             color: 'good',
    //             message: "*Deploy to kubernetes:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}"
    //                 kubeconfig(credentialsId: 'configk8', serverUrl: '') {
    //                     sh 'cat backend-stag/be-stag.yaml | sed "s/{{NEW_TAG}}/0.0.$BUILD_NUMBER-staging/g" | kubectl apply -f -'
    //                 }
    //             }
    //             else if (env.BRANCH_NAME == 'master') {
    //                 slackSend channel: '#jenkinsnotif',
    //             color: 'good',
    //             message: "*Deploy to kubernetes:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}"
    //                 kubeconfig(credentialsId: 'configk8', serverUrl: '') {
    //                     sh 'cat backend-prod/be-prod.yaml | sed "s/{{NEW_TAG}}/0.0.$BUILD_NUMBER-master/g" | kubectl apply -f -'
    //                 }
    //             }
    //             else {
    //                 echo "Tidak ada yg dideploy"
    //             }
    //         }
    //     }
    // } 
}
     post {
            success {
                slackSend channel: '#jenkinsnotif',
                color: 'good',
                message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n More info at: ${env.BUILD_URL}",
            }    

            failure {
                slackSend channel: '#jenkinsnotif',
                color: 'danger',
                message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n More info at: ${env.BUILD_URL}"
                }

        }
       
}


