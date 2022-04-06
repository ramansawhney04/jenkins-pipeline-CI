pipeline {
    agent any
	// agent { docker { image 'maven:3.6.3'} }
	// agent { docker { image 'node:13.8'} }
    environment {
        dockerHome = tool 'mydocker'
	    mavenHome = tool 'my-mvn'
	    PATH = "$dockerHome/bin:$mavenHome/bin:$PATH"
    }
    stages {

	  stage ('Build') {
	    steps {
		  //sh 'mvn --version'
		  //sh 'node --version'
		  echo "Build"
		  echo "PATH - $PATH"
		  echo "BUILD_NUMBER - $env.BUILD_NUMBER"
		  echo "BUILD_ID - $env.BUILD_ID"
		  echo "JOB_NAME - $env.JOB_NAME"
		  echo "BUILD_TAG - $env.BUILD_TAG"
		  echo "BUILD_URL - $env.BUILD_URL"
        }
      }

      stage ('Compile') {
        steps {
          sh "mvn clean compile"
		    }
	  }
	
      stage ('Test') {
        steps {
          sh "mvn test"
        }
      }
	
	  stage ('Integration Test') {
	    steps {
	      sh "mvn failsafe:integration-test failsafe:verify"
	      }
	  }
	
	  stage ('Package') {
	    steps {
	      sh "mvn package -DskipTests"
	      }
	  }
	
      stage ('Build Docker Image') {
	    steps {
                //sh "docker build -t ramansawhney04/currency-exchange-devops:$env.BUILD_TAG"
		  script {
	          dockerImage = docker.build("ramansawhney04/currency-exchange-devops:${env.BUILD_TAG}")   
		       }
	        }
	  }
      stage ('Push Docker Image') {
	    steps {
                //sh "docker build -t ramansawhney04/currency-exchange-devops:$env.BUILD_TAG"
		  script {
		      docker.withRegistry('', 'dockerhub-creds') {    
	            dockerImage.push();
			    dockerImage.push('latest');
			      }
		      }
	      } 
      }
	  stage('Deploy App on K8s') {
      steps {
        script {
          kubernetesDeploy(configs: "*.yaml", "service.yaml" kubeconfigId: "mykubeconfig")
          }
        }
      } 
	}	  
}

