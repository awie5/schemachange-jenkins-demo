#!/usr/bin/env groovy
pipeline {
  agent {label 'your-docker-agent-name-here'}
  environment {
    VIRTUALENV                  = 'snowflake_dev'                           // name of virtual environment
    PYTHON_VERSION              = 'python3.7'                               // can be python3.8, python3.9
    PIP_VERSION                 = 'pip3.7'                                  // can be pip3.8, pip3.9
    PROJECT_FOLDER              = 'migrations'                              // name of project folder where scripts are located
    SF_ACCOUNT                  = credentials('SF_ACCOUNT')        // typically everything that comes before snowflakecomputing.com
    SF_USER                     = credentials('SF_USER')
    SF_ROLE                     = credentials('SF_ROLE')                       // Typically requires create, update, delete privileges
    SF_WH                       = credentials('SF_WH')
    SF_DB                       = credentials('SF_DB')
    SF_CH                       = credentials('SF_CH')
    SECRET_LOCATION             = '/home/awie'              // If you are using a Key Pair Authentication
    JENKINS_CRED_ID_SECRET_FILE = 'rsa_key.p8'      // If you are using a Key Pair Authentication
    JENKINS_CRED_ID_SECRET      = 'matofu30'  // If you are using a Key Pair Authentication
    SCHEMACHANGE                = "${WORKSPACE}/${VIRTUALENV}/lib/${PYTHON_VERSION}/site-packages/schemachange/cli.py"
  }
  stages {
    stage('Deploying Changes To Sandbox Snowflake') {
      steps {
        withCredentials([file(credentialsId: "${JENKINS_CRED_ID_SECRET_FILE}", variable: 'SNOWFLAKE_PRIVATE_KEY_FILE'),
                         string(credentialsId: "${JENKINS_CRED_ID_SECRET}", variable: 'SNOWFLAKE_PRIVATE_PASSPHRASE')]) {
          writeFile file: "${SECRET_LOCATION}", text: readFile(SNOWFLAKE_PRIVATE_KEY_FILE)
          sh """#!/bin/bash -x
                  echo "PROJECT_FOLDER ${PROJECT_FOLDER}"
                  echo 'Step 1: Installing schemachange'
                  virtualenv ${VIRTUALENV} -p ${PYTHON_VERSION}
                  source ${VIRTUALENV}/bin/activate
                  ${PIP_VERSION} install schemachange --upgrade
                  export SNOWFLAKE_PRIVATE_KEY_PATH=${SECRET_LOCATION}
                  export SNOWFLAKE_PRIVATE_KEY_PASSPHRASE=${SNOWFLAKE_PRIVATE_PASSPHRASE}
                  echo 'Step 2: Running schemachange' 
                  ${PYTHON_VERSION} ${SCHEMACHANGE} -f ${PROJECT_FOLDER} -a ${SF_ACCOUNT} -u ${SF_USER} -r ${SF_ROLE} -w ${SF_WH} -d ${SF_DB} -c ${SF_CH} -v
           """
        }
      }
    }
  }
}
