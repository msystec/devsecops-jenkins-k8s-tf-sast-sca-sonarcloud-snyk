pipeline {
  agent any
  tools { 
        maven 'Maven_3_5_2'  
    }
   stages{
     stage ('Check-Git-Secrets') {
      steps {
        sh 'rm trufflehog || true'
        sh 'docker run gesellix/trufflehog --json https://github.com/msystec/jenkins-webapp.git > trufflehog'
        sh 'cat trufflehog'
      }
    }
    stage('CompileandRunSonarAnalysis') {
            steps {	
		sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=newdevsecopswebapp -Dsonar.organization=newdevsecopswebapp -Dsonar.host.url=https://sonarcloud.io -Dsonar.token=5f2568bcbc73ab543ace7219c7c24b55b1d99612'
			}
    }
    stage('RunSCAAnalysisUsingSnyk') {
            steps {		
				withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
					sh 'mvn snyk:test -fn'
				}
			}
    
    }
    stage('Build') { 
            steps { 
               withDockerRegistry([credentialsId: "dockerlogin", url: ""]) {
                 script{
                 app =  docker.build("devsecops")
                 }
               }
            }
    }

    stage('Push') {
            steps {
                script{
                    docker.withRegistry('https://992382390547.dkr.ecr.us-east-1.amazonaws.com/', 'ecr:us-east-1:aws-credentials') {
                    app.push("latest")
                    }
                }
            }
    	}
	  }
}
