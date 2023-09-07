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
                    // Ensure that you have configured your Kubeconfig file manually
                    // If you haven't, you can configure it using 'aws eks update-kubeconfig' command

                    // Set the KUBECONFIG environment variable to the path of your Kubeconfig file
                    def kubeconfigPath = "/.kube/config"
                    env.KUBECONFIG = kubeconfigPath

                    // Verify that the KUBECONFIG variable is set correctly
                    echo "KUBECONFIG set to: ${env.KUBECONFIG}"

                    // List available contexts in the KUBECONFIG file
                    sh "kubectl config get-contexts"

                    // Setting context
                    sh "aws eks update-kubeconfig --name ${clusterName} --region ${awsRegion}"

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
