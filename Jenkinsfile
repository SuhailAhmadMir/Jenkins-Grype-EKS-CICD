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
                
                // Define the cluster name (you can set this dynamically)
                def clusterName = "fleetman" // Replace with your cluster name

                // Update kubeconfig for the specified cluster
                sh "aws eks update-kubeconfig --name ${clusterName} --region us-west-2" // Replace 'us-west-2' with your region

                // Temporary path for kubeconfig
                def kubeconfigPath = "/home/ec2-user/.kube/config"

                // Verify that the KUBECONFIG variable is set correctly
                echo "KUBECONFIG set to: ${kubeconfigPath}"

                // List available contexts in the KUBECONFIG file
                sh "kubectl config get-contexts"
                sh "pwd"
                sh "ls -l /var/lib/jenkins/workspace/paac-demo/kubefiles"

                // Specify the path to the vproappdep.yaml file
                def yamlFilePath = "/var/lib/jenkins/workspace/paac-demo/kubefiles/vproappdep.yml"

                // Now, you can deploy your workloads to EKS using 'kubectl apply'
                sh "kubectl apply -f ${yamlFilePath}"

               
                }
            }
        }

    }

  }
}
