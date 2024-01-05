node(){
    
def containerName="insurance"
def tag="latest"
def dockerHubUser="nikitaram2799"
def mvnHome = tool name: 'maven3', type: 'maven'

        stage('prepare enviroment'){
          
            tagName="latest"
        }
        
        stage('git code checkout'){
            try{
                echo 'checkout the code from git repository'
                git 'https://github.com/nikitaram882799/star-agile-insurance-project.git'
            }
            catch(Exception e){
                echo 'Exception occured in Git Code Checkout Stage'
                currentBuild.result = "FAILURE"
                emailext body: '''Dear All,
                The Jenkins job ${JOB_NAME} has been failed. Request you to please have a look at it immediately by clicking on the below link. 
                ${BUILD_URL}''', subject: 'Job ${JOB_NAME} ${BUILD_NUMBER} is failed', to: 'nikitaram2799@gmail.com'
            }
        }
    
        stage('Build the Application'){
            echo "Cleaning... Compiling...Testing... Packaging..."
            //sh 'mvn clean package'
            sh "${mvnHome}/bin/mvn clean install"        
        }
    
       /* stage('publish test reports'){
             publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: '/var/lib/jenkins/workspace/Insurance/target/surefire-reports', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: '', useWrapperFileDirectly: true])
        }*/
         stage("Image Prune"){
             sh "docker image prune -f"
        }

    
         stage('Image Build'){
            sh "docker build -t $containerName:$tag --pull --no-cache ."
            echo "Image build complete"
        }
    

        stage('Push to Docker Registry'){
            withCredentials([usernamePassword(credentialsId: 'DockerHubCreds', passwordVariable: 'dockerPassword', usernameVariable: 'dockeruser')]) {
                sh "docker login -u $dockerUser -p $dockerPassword"
                sh "docker tag $containerName:$tag $dockerUser/$containerName:$tag"
                sh "docker push $dockerUser/$containerName:$tag"
                echo "Image push complete"
            }
        }
     
        stage('Kubernetes deployment') {
            kubernetesDeploy configs: 'deploymentservice.yaml', kubeConfig: [path: ''], kubeconfigId: 'KubeConfig', secretName: '', ssh: [sshCredentialsId: '*', sshServer: ''], textCredentials: [certificateAuthorityData: '', clientCertificateData: '', clientKeyData: '', serverUrl: 'https://']
        }
    }
