pipeline {
    agent any
    environment {
        fimg = "frontend_image"
        bimg = "backend_image"
        fcont = "frontend_container"
        bcont = "backend_container"
        dbcont = "mongodb"
        net_name = "mern_docker_network_ci_cd"
        f_port = 5173
        b_port = 5050
        db_port = 27017
    }

    stages {
        stage('SCM Checkout') {
            steps {
                git branch: 'compose', url: 'https://github.com/SyedShakirX/MERN-docker-compose'
            }
        }
        stage('Build Frontend , Backend , Database & a Network') {
            steps {
                sh '''
                    docker build -t ${fimg} mern/frontend
                    docker build -t ${bimg} mern/backend
                    ##
                    ## We can (should) push our images to ECR or Dockerhub, But this is a demo project we dont want to do that,
                    ##
                    docker pull mongo:6
                    docker network inspect ${net_name} >/dev/null 2>&1 || docker network create ${net_name}
                '''
            }
        }
        stage ('Run Frontend , Backend & Database') {
            steps {
                sh '''
                    #Stop the pre existing containers
                    docker stop ${fcont} && docker rm ${fcont} || true
                    docker stop ${bcont} && docker rm ${bcont} || true
                    docker stop ${dbcont} && docker rm ${dbcont} || true

                '''

                sh '''
                    #Run the new containers
                    docker run --name=${fcont} -d -p ${f_port}:${f_port} --network=${net_name} ${fimg}
                    docker run --name=${dbcont} -d -p ${db_port}:${db_port} --network=${net_name} mongo:6
                    sleep 10
                    docker run --name=${bcont} -d -p ${b_port}:${b_port} --network=${net_name} ${bimg}

                '''
            }
        }
    }


}
