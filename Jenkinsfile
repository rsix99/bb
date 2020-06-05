pipeline {
  agent any
  environment {
    registry = "rubensix/quintordemo"
    name = "${JOB_NAME}"
  }
  stages {
    stage('checkout project') {
      steps {
        checkout scm
      } }

      stage('check env') {
      steps {
        sh "mvn -v"
        sh "java -version"
      } }

    stage('test') {
      steps {
        sh "mvn test"
      }} 
	    
    stage('package') {
      steps {
        sh "mvn package"
      } }
     
    
    stage('Publish') {
           environment {
               registryCredential = 'dockerhub'
           }
           steps{
               script {
                 if ("${scm.branches[0].name}" == '*/master') {
                   def appimage = docker.build registry + ":${BUILD_NUMBER}"
                   docker.withRegistry( '', registryCredential ) {
                       appimage.push()
                     appimage.push('latest')}}
                else { 
                  echo "Joe"
                  echo "${env.BRANCH_NAME}"
                   }
               }
           }
       }
    stage ('Deploy') {
           steps {
               script{
                 if("${scm.branches[0].name}" == '*/master') {
		   sh "helm upgrade --install --namespace=${name} --set image.repository=${registry} --set image.tag=${BUILD_NUMBER} ${name} ./helm"}
                 else { 
                  echo 'Dussss deploy'
                 }}
           }
    }
    stage('Tag Build') {
       environment {
         GIT_AUTH = credentials('github')
         }
       steps {
           sh 'git tag -a build${BUILD_NUMBER} -m "Tag for jenkins build ${BUILD_NUMBER}"'
           sh 'git config --local credential.helper "!f() { echo username=\\$GIT_AUTH_USR; echo password=\\$GIT_AUTH_PSW; }; f"'
           sh 'git push origin build${BUILD_NUMBER}'
            }
        }
}
}


