pipeline{
    agent any
    
    environment {
        DOCKER_REPO = "musatee11/springboot"
        //DOCKERHUB_CRED = credentials('dockerhub_cred')
    } 
   
    stages{
        stage("Cloning Repo"){
            steps{
                git 'https://github.com/musatee/test_springboot_cicd.git'
            }
        }
        /* using docker container as agent instead of installing plugins on node. NOTE: current workspace will be mounted inside container 
        & no new workspace will be created. so everyting from current WS is mounted with container . It's done by mentioning "reuseNode" to true which 
        overrides default behavior of docker container agent */
        stage("Building Artifact"){
            agent {
                docker {
                    image 'maven:3.8.7-openjdk-18' 
                    reuseNode true
                }
            } 
            steps{
               sh 'mvn clean install'
            }
        } // end of building artifact stage
        stage("Building Image & committing to dockerHub"){
            steps{
                script{
                    docker.withRegistry('https://index.docker.io/v1/','dockerhub_cred') {
                        def app_image = docker.build("${DOCKER_REPO}:${env.BUILD_ID}")
                        app_image.push()
                        }
                }
                
                
            }
            post {
                success {
                    sh "docker rmi -f ${DOCKER_REPO}:${env.BUILD_ID}"
                }
            }

        }
       
    }
   post{
        
        always {
            cleanWs()
        }
    } 
}
