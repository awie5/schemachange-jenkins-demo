#!/usr/bin/env groovy
pipeline {
  agent any
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
  }
  stages{
    stage('Deploying Changes To Sandbox Snowflake') {
      steps {
          sh """#!/bin/bash -x
                  echo "PROJECT_FOLDER ${PROJECT_FOLDER}"
                  echo 'Step 1: Installing schemachange'
                  virtualenv ${VIRTUALENV} -p ${PYTHON_VERSION}
                  source ${VIRTUALENV}/bin/activate
                  ${PIP_VERSION} install schemachange --upgrade
                  echo 'Step 2: Running schemachange' 
                  ${PYTHON_VERSION} ${SCHEMACHANGE} -f ${PROJECT_FOLDER} -a ${SF_ACCOUNT} -u ${SF_USER} -r ${SF_ROLE} -w ${SF_WH} -d ${SF_DB} -c ${SF_CH} -v
           """
       }
     }
   }
}
