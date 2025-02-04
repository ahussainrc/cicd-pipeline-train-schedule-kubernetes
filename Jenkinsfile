pipeline {
    agent any
    environment {
        //be sure to replace "willbla" with your own Docker Hub username
        DOCKER_IMAGE_NAME = "ahussain19/train-schedule"
    }
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Build Docker Image') {
            when {
                branch 'master'
            }
            steps {
                script {
                    app = docker.build(DOCKER_IMAGE_NAME)
                    app.inside {
                        sh 'echo Hello, World!'
                    }
                }
            }
        }
        //stage('Push Docker Image') {
        //    when {
        //        branch 'master'
        //    }
        //    steps {
        //        script {
        //            docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
        //                app.push("${env.BUILD_NUMBER}")
        //                app.push("latest")
        //            }
        //        }
        //    }
        //}
        stage('DeployToProduction') {
            when {
                branch 'master'
            }
            steps {
                //input 'Deploy to Production?'
                milestone(1)
                //sh "kubectl apply -f train-schedule-kube.yml "
                kubernetesDeploy(
                    kubeconfigId: 'kubeconfig',
                    //configs: 'train-schedule-kube.yml',
                    configs: 'pod.yml',
                    enableConfigSubstitution: true
                )
            }
        }
    }
}
