pipeline {  
  agent any 
  options {
    buildDiscarder(logRotator(numToKeepStr: '5'))   
  }
  stages {
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
    //  stage('Deploy to Kubernetes') {
    //     steps {
    //         script {
    //             if (env.BRANCH_NAME == 'staging') {
    //                 kubeconfig(credentialsId: 'kubernetesconfig', serverUrl: '') {
    //                     sh 'cat backend-stag/be-stag.yaml | sed "s/{{NEW_TAG}}/0.0.$BUILD_NUMBER-staging/g" | kubectl apply -f -'
    //                 }
    //             }
    //             else if (env.BRANCH_NAME == 'master') {
    //                 kubeconfig(credentialsId: 'kubernetesconfig', serverUrl: '') {
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
    //  post {
    //         success {
    //             slackSend channel: '#project',
    //             color: 'good',
    //             message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n More info at: ${env.BUILD_URL}"
    //         }    

    //         failure {
    //             slackSend channel: '#project',
    //             color: 'danger',
    //             message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n More info at: ${env.BUILD_URL}"
    //             }

    //     }
       
}