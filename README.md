# CICDAPICatalogDemo
CI/CD Demo to demonstrate how to configure API Cataloging in Jenkins so that you are able to modify the CI script to trigger the descriptor file and update the API asset from the Jenkins pipeline

# Demo - API Catalog

I will demonstrate how to Catalog APIs locally from your computer by showing you how to install the API Catalog CLI tool, how to run the API Catalog CLI on your system terminal, how to create a descriptor file, as well as, mention the APIs to Catalog, and finally I will show you how to Publish the API via the publish asset command locally from your computer.

I will also demonstrate, how you can run the tool in a CI/CD pipeline, such as Jenkins, by showing you how you can modify the CI script to trigger the descriptor file and catalog or update API assets from your pipeline.

## Install Node JS and npm locally

    sudo npm install -g npm

Go to https://nodejs.org

## Validate Installation of Node JS and NPM package

    node -v  
    npm -v

## Install api-catalog

    sudo npm install -g api-catalog-cli@latest

## Validate installation

    api-catalog

  

## Create connected app

  https://docs.mulesoft.com/access-management/connected-apps-overview

## Configure the api-catalog CLI parameters

    api-catalog conf client_id <client_id>  
    api-catalog conf client_secret <client_secret>  
    api-catalog conf host anypoint.mulesoft.com 
    api-catalog conf organization <org-id>

## Create api-catalog CLI Parameters using Username and Password

    api-catalog conf username <username>  
    api-catalog conf password <password>  
    api-catalog conf organization <org-id>

  

*Make sure that the user has "API CATALOG CONTRIBUTOR" permission*

## Get help on Conf and locate the conf.json file

    api-catalog conf —help  

## Create the api descriptor file to discover APIs

    api-catalog create-descriptor -d catalog.yaml

## Create default name for the api descriptor file

    api-catalog create-descriptor

*Make sure this is executed inside the directory where the asset resides*

## Publish asset to exchange

    api-catalog publish-asset -d catalog.yaml

## Using Trigger Conditions

Add to the descriptor file  

    triggerConditions:  
    version:  
    	-1.0.0

api-catalog publish-asset —trigger-criteria=version:1.0.0

## Using Version Criteria Conditions

Add to the descriptor file  

    versionStrategyConditions:  
          minorIncrease:  
            branches:  
              - main
|

  

     api-catalog publish-asset --version-strategy-criteria=branch:main

## Example of API Descriptor

    #%Catalog Descriptor 1.0  
      
    
        triggerConditions:  
          version:  
            - 1.0.0  
           
        versionStrategyConditions:  
              minorIncrease:  
                branches:  
                  - main  
                
        projects:  
          - main: acme-twillio-sms-system-api-1.0.0-raml\acme-twillio-sms-system-api.raml  
            assetId: acme-twillio-sms-system-api  
            version: 1.0.0  
            apiVersion: v1

## Example of JenkinsFile / Groovy Pipeline Script

    pipeline {
        agent any
         tools {nodejs "NPM"}
        
        stages {
            stage('git checkout') {
                steps {
                    git branch: "main", url: 'https://github.com/linabeltheengineer/CICDAPICatalogDemo.git'
                    sh 'ls'
                }
            }
            stage('Build Artifacts') {    
                steps {
                     // building
                      sh 'ls'
                }
            }
            stage('API Cataloging') {    
                steps 
                    { 
                        sh 'npm install'
                        sh 'api-catalog conf username API-CATALOG-DEMO'
                        sh 'api-catalog conf password acdDemo2022'
                        sh 'api-catalog conf organization b22c9db6-5750-4ffc-a25a-793d95e35c54'
                        sh 'api-catalog publish-asset'
                    }
                }
            
             stage('Deploy') {    
                steps {
                    // lots of deploying 
                     sh 'ls'
                }
            }
    }
    }
