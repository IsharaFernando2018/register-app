// pipeline {
//     agent { label 'Jenkins-Agent' }
//     tools {
//         jdk 'Java17'
//         maven 'Maven3'
//     }
//     environment {
// 	    APP_NAME = "register-app-pipeline"
//             RELEASE = "1.0.0"
//             DOCKER_USER = "ferdi2018"
//             //DOCKER_PASS = 'dockerhub'
//             IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
//             IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
// 	    // JENKINS_API_TOKEN = credentials("JENKINS_API_TOKEN")
//     }
	
//     stages {
//         stage("Cleanup Workspace") {
//             steps {
//                 cleanWs()
//             }
//         }

//         stage("Checkout from SCM") {
//             steps {
//                 git branch: 'main', credentialsId: 'github', url: 'https://github.com/IsharaFernando2018/register-app.git'
//             }
//         }

//         stage("Build Application") {
//             steps {
//                 bat "mvn clean package"
//             }
//         }

//         stage("Test Application") {
//             steps {
//                 bat "mvn test"
//             }
//         }
	    
//         stage("SonarQube Analysis"){
//            steps {
// 	           script {
// 		        withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token') { 
//                         bat "mvn sonar:sonar"
// 		        }
// 	           }	
//            }
//        }

// 	stage("Cleanup Old Containers") {
//     		steps {
//         		bat 'docker-compose down --remove-orphans || exit 0'
//     		}
// 	}


// 	stage("Start Services PostgreSQL") {
//       		steps {
//         	     bat 'docker-compose up -d' 
//       	      	}
//     	}
	    
// 	stage("Quality Gate"){
//            steps {
//                script {
//                     waitForQualityGate abortPipeline: false, credentialsId: 'jenkins-sonarqube-token'
//                 }	
//             }

//         }
// 	stage("Build & Push Docker Image") {
//             steps {
//                 script {
//                     docker.withRegistry('',DOCKER_PASS) {
//                         docker_image = docker.build "${IMAGE_NAME}"
//                     }

//                     docker.withRegistry('',DOCKER_PASS) {
//                         docker_image.push("${IMAGE_TAG}")
//                         docker_image.push('latest')
//                     }
//                 }
//             }

//        }
// 	stage("Trivy Scan") {
//            steps {
//                script {
// 	            bat ('docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image ferdi2018/register-app-pipeline:latest --no-progress --scanners vuln  --exit-code 0 --severity HIGH,CRITICAL --format table')
//                }
//            }
//        }

//  //       stage ('Cleanup Artifacts') {
//  //           steps {
//  //               script {
//  //                    sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
//  //                    sh "docker rmi ${IMAGE_NAME}:latest"
//  //               }
//  //          }
//  //       }
// 	// stage("Trigger CD Pipeline") {
//  //            steps {
//  //                script {
//  //                    sh "curl -v -k --user ishara:${JENKINS_API_TOKEN} -X POST -H 'cache-control: no-cache' -H 'content-type: application/x-www-form-urlencoded' --data 'IMAGE_TAG=${IMAGE_TAG}' 'ec2-18-206-121-209.compute-1.amazonaws.com:8080/job/gitops-register-app-cd/buildWithParameters?token=gitops-token'"
//  //                }
//  //            }
//  //       }
	    
//     }
// }

//////////////////////////////////////////////////////////////////////////////////////////////////////////////
/////////////////////////////       2nd Method   /////////////////////////////////////////////////////////////
//////////////////////////////////////////////////////////////////////////////////////////////////////////////
pipeline {
    agent { label 'docker-agent' }
    tools {
        jdk 'Java17'
        maven 'Maven3'
    }

 //    environment {
	// APP_NAME = "update-register-app-pipeline"
 //        RELEASE = "1.0.0"
 //        DOCKER_USER = "ferdi2018"
 //        DOCKER_PASS = 'dockerhub'
 //        IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
 //        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
	// // JENKINS_API_TOKEN = credentials("JENKINS_API_TOKEN")
 //    }
	environment {
    		DOCKER_CREDENTIALS = 'dockerhub'  // your Jenkins credentials ID for Docker Hub
    		APP_NAME = "update-register-app-pipeline"
    		RELEASE = "1.0.0"
    		IMAGE_NAME = "ferdi2018/update-register-app-pipeline"
    		IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
	}

    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout from SCM") {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/IsharaFernando2018/register-app.git'
            }
        }

        stage("Build Application") {
            steps {
                sh "mvn clean package"
            }
        } 

	stage("Test Application") {
            steps {
                sh "mvn test"
            }
        }

	stage("SonarQube Analysis"){
           steps {
	        script {
		    withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token') { 
                    sh "mvn sonar:sonar"
		   }
	        }	
           }
       }

       stage("Quality Gate") {
    	    steps {
        	timeout(time: 2, unit: 'MINUTES') {
           	   waitForQualityGate abortPipeline: true
        	}
    	     }
       }

	// stage("Build & Push Docker Image") {
 //            steps {
 //                script {
 //                    docker.withRegistry('',DOCKER_PASS) {
 //                        docker_image = docker.build "${IMAGE_NAME}"
 //                    }

 //                    docker.withRegistry('',DOCKER_PASS) {
 //                        docker_image.push("${IMAGE_TAG}")
 //                        docker_image.push('latest')
 //                    }
 //                }
 //            }
 //       }
	 //    stage("Build & Push Docker Image") {
  //   		agent { label 'docker-agent2' }  // 👈 Run this stage only on docker-agent2
  //   			steps {
  //       			script {
  //           				docker.withRegistry('', env.DOCKER_CREDENTIALS) {
  //               			def docker_image = docker.build("${IMAGE_NAME}")
  //               			docker_image.push("${IMAGE_TAG}")
  //               			docker_image.push("latest")
  //           				}
  //       			}
  //   			}
		// }
	    stage("Build & Push Docker Image") {
    		agent { label 'docker-agent2' }
    			steps {
        			script {
            				docker.withRegistry('', env.DOCKER_CREDENTIALS) {
                			def docker_image = docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                			docker_image.push()
                			docker_image.push("latest")
            				}
        			}		
    			}
	}



	    
    }
}


