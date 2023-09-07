pipeline {
    agent any
    tools {
        maven 'MAVEN3'
    }
    environment {
        registry = '729590520513.dkr.ecr.us-west-2.amazonaws.com/hellodatarepo'
        dockerimage = ''
        awsRegion = 'us-west-2'
        clusterName = 'fleetman'
    }
    stages {
        stage('Checkout the project') {
            steps {
                git branch: 'docker', url: 'https://github.com/devopshydclub/vprofile-project.git'
            }
        }

        stage('Build the package') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Deploy to Amazon EKS') {
            steps {
                script {
            // Retrieve AWS credentials from Jenkins credentials with ID 'awscreds'
            withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'awscreds', accessKeyVariable: 'AWS_ACCESS_KEY_ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
                // Set the AWS credentials for this session
                sh """
                    aws configure set aws_access_key_id \${AWS_ACCESS_KEY_ID}
                    aws configure set aws_secret_access_key \${AWS_SECRET_ACCESS_KEY}
                """
                
                // Set the KUBECONFIG environment variable to the path of your updated kubeconfig file
                def kubeconfigPath = "/home/ec2-user/.kube/config"
                env.KUBECONFIG = kubeconfigPath

                // Verify that the KUBECONFIG variable is set correctly
                echo "KUBECONFIG set to: \${env.KUBECONFIG}"

                // List available contexts in the updated KUBECONFIG file
                sh "kubectl config get-contexts"

                // Now, you can deploy your workloads to EKS using 'kubectl apply'
                sh "kubectl apply -f workloads.yaml"
                }
            }
        }
    }
    post {
        success {
            // Clean up the downloaded kubeconfig file
            sh "rm -f ${env.KUBECONFIG}"
        }
    }
}
