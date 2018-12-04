pipeline {
  agent {
    label 'mule-builder'
  }
  environment {
    DEPLOY_CREDS = credentials('deploy-anypoint-user')
    MULE_VERSION = '4.1.2-AM'
    BG = "1Platform\\Retail\\Sales"
    WORKER = "Small"
  }
  stages {
    stage("SAP Libs Preparation") {
      steps {
        sh "mkdir saplibs"
        sh 'cd ./saplibs'
        git credentialsId: '233846f8-9482-458e-b3fe-1744f4c224e5', url: 'https://github.com/mulesoft-consulting/sap-libs-internal.git'
      }
    }
    stage('install SAP Libs') {
      steps {
        sh "mvn -B install:install-file -DgroupId=com.sap.conn.idoc -DartifactId=com.sap.conn.idoc.sapidoc3 -Dversion=3.0.17 -Dpackaging=jar -Dfile=${WORKSPACE}/3.0.17/sapidoc3.jar"
        sh "mvn -B install:install-file -DgroupId=com.sap.conn.jco -DartifactId=com.sap.conn.jco.sapjco3 -Dversion=3.0.17 -Dpackaging=jar -Dfile=${WORKSPACE}/3.0.17/sapjco3.jar"
        sh "mvn -B install:install-file -DgroupId=com.sap.conn.jco -DartifactId=com.sap.conn.jco.libsapjco3 -Dversion=3.0.17 -Dclassifier=external-library -Dpackaging=jnilib -Dfile=${WORKSPACE}/3.0.17/darwinintel64/libsapjco3.jnilib"
        sh "mvn -B install:install-file -DgroupId=com.sap.conn.jco -DartifactId=com.sap.conn.jco.libsapjco3 -Dversion=3.0.17 -Dclassifier=external-library -Dpackaging=so -Dfile=${WORKSPACE}/3.0.17/linuxx86_64/libsapjco3.so"
        sh "mvn -B install:install-file -DgroupId=com.sap.conn.jco -DartifactId=com.sap.conn.jco.libsapjco3 -Dversion=3.0.17 -Dclassifier=external-library -Dpackaging=dll -Dfile=${WORKSPACE}/3.0.17/NTAMD64/sapjco3.dll"
        sh 'cd ..'
        sh 'rm -rf ./saplibs'
      }
    }

    stage('Preparation') {
      steps {
        configFileProvider([configFile(fileId: "${BRANCH_NAME}-sys-order-api-for-sap.yaml", replaceTokens: true, targetLocation: './src/main/resources/config/configuration.yaml')]) {
          sh 'echo "Branch NAME: $BRANCH_NAME"'
          sh 'ls -ltrh'
        }

      }
    }
 //    stage('Build') {
 //      steps {
 //        sh 'mvn -B clean package -DskipTests'
 //      }
 //    }
 //
 //    stage('Test') {
 //      steps {
 // withMaven(
 //               mavenSettingsConfig: 'f007350a-b1d5-44a8-9757-07c22cd2a360'){
 //                    sh "mvn -B test"
 //              }
 //      }
 //    }
 //
 //    stage('Deploy Development') {
 //      when {
 //        branch 'develop'
 //      }
 //      environment {
 //        ENVIRONMENT = 'Development'
 //        ANYPOINT_ENV = credentials('DEV_ANYPOINT_SALES')
 //        APP_NAME = 'dev-nto-order-api-for-sap-v1'
 //      }
 //      steps {
 //        sh 'mvn -V -B -DskipTests deploy -DmuleDeploy -Dmule.version=$MULE_VERSION -Danypoint.username=$DEPLOY_CREDS_USR -Danypoint.password=$DEPLOY_CREDS_PSW -Dcloudhub.app=$APP_NAME -Dcloudhub.environment=$ENVIRONMENT -Denv.ANYPOINT_CLIENT_ID=$ANYPOINT_ENV_USR -Denv.ANYPOINT_CLIENT_SECRET=$ANYPOINT_ENV_PSW -Dcloudhub.bg=$BG -Dcloudhub.worker=$WORKER'
 //      }
 //    }
 //    stage('Deploy Production') {
 //        when {
 //          branch 'master'
 //        }
 //        environment {
 //          ENVIRONMENT = 'Production'
 //          ANYPOINT_ENV = credentials('PRD_ANYPOINT_SALES')
 //          APP_NAME = 'nto-order-api-for-sap-v1'
 //        }
 //        steps {
 //          sh 'mvn -V -B -DskipTests deploy -DmuleDeploy -Dmule.version=$MULE_VERSION -Danypoint.username=$DEPLOY_CREDS_USR -Danypoint.password=$DEPLOY_CREDS_PSW -Dcloudhub.app=$APP_NAME -Dcloudhub.environment=$ENVIRONMENT -Denv.ANYPOINT_CLIENT_ID=$ANYPOINT_ENV_USR -Denv.ANYPOINT_CLIENT_SECRET=$ANYPOINT_ENV_PSW -Dcloudhub.bg=$BG -Dcloudhub.worker=$WORKER'
 //        }
 //    }
  }

  tools {
    maven 'M3'
    git 'git'
  }
}
