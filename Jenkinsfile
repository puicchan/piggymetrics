node {
  stage('init') {
    checkout scm
  }
  stage('build') {
    sh 'mvn clean package'
  }
  stage('deploy') {
    withCredentials([azureServicePrincipal('azure_service_principal')]) {
      // login Azure
      sh '''
        az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
        az account set -s $AZURE_SUBSCRIPTION_ID
      '''  
      // Set default resource group name and cluster name
      sh 'az configure --defaults group=pc-spring-c'
      sh 'az configure --defaults spring-cloud=pcsc'
	  // Deploy applications
      sh 'az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar'
      sh 'az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar'
      sh 'az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar'
      sh 'az logout'
    }
  }
}
