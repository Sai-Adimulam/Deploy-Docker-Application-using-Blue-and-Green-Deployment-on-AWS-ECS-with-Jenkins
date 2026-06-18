pipeline{
    agent any

    environment {
        AWS_REGION='ap-south-1'
        AWS_ACCOUNT_ID='908708651361'
        IMAGE_TAG="${BUILD_NUMBER}"
        
    }
     stages{
        stage('Github'){
            steps{
                git branch:'main',
                     url: 'https://github.com/Sai-Adimulam/Deploy-Docker-Application-using-Blue-and-Green-Deployment-on-AWS-ECS-with-Jenkins'
            }
        }
        stage('Build Docker Image'){
            steps{
              sh  'docker build -t app-1 .'
            }
        }
        stage('AWS Authenticate ECR'){
          steps{
           sh 'aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 908708651361.dkr.ecr.ap-south-1.amazonaws.com'
          }
        }
        stage('Tag Image'){
            steps{
                sh 'docker tag app-1 908708651361.dkr.ecr.ap-south-1.amazonaws.com/app-1:latest'
            }
        }
        stage('Push Image'){
            steps{
               sh 'docker push 908708651361.dkr.ecr.ap-south-1.amazonaws.com/app-1:latest'
            }
        }
        stage('Deploy on Ecs'){
            steps{
                sh '''
                aws ecs update-service \
                --cluster app-1-cluster \
                --service app-1-green-service \
                --force-new-deployment \
                --region ap-south-1
                '''
            }
        }
        stage('wait for the green task'){
            steps{
                sh 'sleep 120'
            }
        }
        stage('switch traffic to greeen'){
          steps{
            sh '''
            aws elbv2 modify-listener \
            --listener-arn arn:aws:elasticloadbalancing:ap-south-1:908708651361:listener/app/app-1-Load-Balancer/744800a557769e09/795b1575786223f3
            --default-actions Type=forward,TargetGroupArn=arn:aws:elasticloadbalancing:ap-south-1:908708651361:targetgroup/app-1-Green/c9a36efda52cca68
            '''
          }
        }
        stage('Cleanup') {
             steps { 
                sh 'docker image prune -f' 
            } 
        }
     }
}

