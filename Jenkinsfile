pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building..'
                // 1. Reference your specific ID here . The ID given in the Managed File for maven settings.xml
                configFileProvider([configFile(fileId: 'mule-maven-setting-from-m2localxml', variable: 'MVN_SETTINGS')]) {
                
                	// 2. Pass the temporary file path to Maven using -s
                	bat 'mvn -B -U -e -V clean package -DskipTests -s %MVN_SETTINGS%'
                
                }      
            }
        }
       
	stage('Test') {
	    steps {
	        echo 'Running MUnit Tests..'
	        configFileProvider([configFile(fileId: 'mule-maven-setting-from-m2localxml', variable: 'MVN_SETTINGS')]) {
	            // We run 'test' goal. 
	            // -DruntimeProduct=mule allows it to run on the community-oriented engine if EE isn't available
	            bat 'mvn test -s %MVN_SETTINGS%'
	        }
	    }
	}
        
        stage('Publish to Exchange') {
        	steps {
        		echo 'Publishing to Exchange ...'
        		configFileProvider([configFile(fileId: 'mule-maven-setting-from-m2localxml', variable: 'MVN_SETTINGS')]) {
                    // Use 'deploy' to push the JAR to Exchange
                    bat 'mvn deploy -DskipTests -s %MVN_SETTINGS%'
                }
        	}
        }
        
        stage('Deploying to CloudHub2.0') {
            steps {
                echo 'Deploying....'
                configFileProvider([configFile(fileId: 'mule-maven-setting-from-m2localxml', variable: 'MVN_SETTINGS')]) {
                
                	// Use 'mule:deploy' to trigger the CloudHub 2.0 deployment
                	bat 'mvn mule:deploy -DmuleDeploy -PDEV -DDEV -DskipTests -s %MVN_SETTINGS%'
                }
                
                 
            }
        }
    }
}