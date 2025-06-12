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
//             DOCKER_PASS = credentials('dockerhub')
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
//                 sh "mvn clean package"
//             }
//         }

//         stage("Test Application") {
//             steps {
//                 sh "mvn test"
//             }
//         }
//         stage("SonarQube Analysis"){
//            steps {
// 	           script {
// 		        withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token') { 
//                         sh "mvn sonar:sonar"
// 		        }
// 	           }	
//            }
//        }
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
//     }
// }
pipeline {
    agent { label 'Jenkins-Agent' }
    tools {
        jdk 'Java17'
        maven 'Maven3'
    }
    environment {
        APP_NAME = "register-app-pipeline"
        RELEASE = "1.0.0"
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

        stage("SonarQube Analysis") {
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
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'jenkins-sonarqube-token'
                }
            }
        }

        stage("Build & Push Docker Image") {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        def imageName = "${DOCKER_USER}/${APP_NAME}"
                        def image = docker.build(imageName)

                        docker.withRegistry('https://index.docker.io/v1/', docker.makeRegistryLoginCredentials(DOCKER_USER, DOCKER_PASS)) {
                            image.push("${IMAGE_TAG}")
                            image.push("latest")
                        }
                    }
                }
            }
        }
    }
}


