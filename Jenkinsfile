pipeline {
  agent {
    label 'mule-builder'
  }
  environment {
    DEPLOY_CREDS = credentials('deploy-anypoint-user')
    MULE_VERSION = '4.1.5'
    BG = "1Platform\\Retail\\Sales"
    WORKER = "Small"
    SAP_CREDS = credentials("$BRANCH_NAME-sys-order-api-for-sap")
  }

  stages{    
    stage('Preparation') {
      steps {
        configFileProvider([configFile(fileId: "${BRANCH_NAME}-sys-order-api-for-sap.yaml", replaceTokens: true, targetLocation: './src/main/resources/config/configuration.yaml')]) {
          withCredentials([file(credentialsId: 'self-signed-keystore.jks', variable: 'KEYSTORE_FILE')]){
            sh 'cp $KEYSTORE_FILE ./src/main/resources/keystore.jks'
            sh 'echo "Branch NAME: $BRANCH_NAME"'

          }
        }
      }
    }
     stage('Build') {
       steps {
         withMaven(
          mavenSettingsConfig: 'f007350a-b1d5-44a8-9757-07c22cd2a360'){
            sh 'mvn -PCloudhub -B clean package -DskipTests'
          }
       }
     }
 
     stage('Test') {
       steps {
          withMaven(
                mavenSettingsConfig: 'f007350a-b1d5-44a8-9757-07c22cd2a360'){
                     sh "mvn -PCloudhub -B test"
          }
       }
     }
 
     stage('Deploy Development') {
       when {
         branch 'develop'
       }
       environment {
         ENVIRONMENT = 'Development'
         ANYPOINT_ENV = credentials('DEV_ANYPOINT_SALES')
         APP_NAME = 'dev-nto-order-api-for-sap-v1'
       }
       steps {
         withMaven(
          mavenSettingsConfig: 'f007350a-b1d5-44a8-9757-07c22cd2a360'){
            sh 'mvn -V -B -PCloudhub -DskipTests deploy -DmuleDeploy -Dmule.version=$MULE_VERSION -Danypoint.username=$DEPLOY_CREDS_USR -Danypoint.password=$DEPLOY_CREDS_PSW -Dcloudhub.app=$APP_NAME -Dcloudhub.environment=$ENVIRONMENT -Denv.ANYPOINT_CLIENT_ID=$ANYPOINT_ENV_USR -Denv.ANYPOINT_CLIENT_SECRET=$ANYPOINT_ENV_PSW -Dcloudhub.bg=$BG -Dcloudhub.worker=$WORKER -Denv.SAP_USER=$SAP_CREDS_USR -Denv.SAP_PSWD=$SAP_CREDS_PSW'
          }
       }
     }

     stage('Deploy Production') {
         when {
           branch 'master'
         }
         environment {
           ENVIRONMENT = 'Production'
           ANYPOINT_ENV = credentials('PRD_ANYPOINT_SALES')
           APP_NAME = 'nto-order-api-for-sap-v1'
         }
         steps {
          withMaven(
            mavenSettingsConfig: 'f007350a-b1d5-44a8-9757-07c22cd2a360'){
            sh 'mvn -V -B -PCloudhub -DskipTests deploy -DmuleDeploy -Dmule.version=$MULE_VERSION -Danypoint.username=$DEPLOY_CREDS_USR -Danypoint.password=$DEPLOY_CREDS_PSW -Dcloudhub.app=$APP_NAME -Dcloudhub.environment=$ENVIRONMENT -Denv.ANYPOINT_CLIENT_ID=$ANYPOINT_ENV_USR -Denv.ANYPOINT_CLIENT_SECRET=$ANYPOINT_ENV_PSW -Dcloudhub.bg=$BG -Dcloudhub.worker=$WORKER -Denv.SAP_USER=$SAP_CREDS_USR -Denv.SAP_PSWD=$SAP_CREDS_PSW'
          }
         }
     }
  }

  post {
      always {
       step([$class: 'hudson.plugins.chucknorris.CordellWalkerRecorder'])
      }
  }

  tools {
    maven 'M3'
    git 'git'
  }
}
