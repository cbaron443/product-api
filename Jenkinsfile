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
	        echo 'Testing and Refreshing Dependencies...'
	        // Reference the same Managed File ID used in your Build stage
	        configFileProvider([configFile(fileId: 'mule-maven-setting-from-m2localxml', variable: 'MVN_SETTINGS')]) {
	            
	            // -B: Batch mode (prevents log clutter)
	            // -U: Force update of dependencies (fixes the "cached 401" issue)
	            // -e: Produce execution error messages (helpful for debugging)
	            bat 'mvn clean test -B -U -e -s %MVN_SETTINGS%'
	            
	        }
	    }
	    post {
	        always {
	            // This captures the MUnit results so Jenkins can show you the success/fail trends
	            junit '**/target/surefire-reports/*.xml'
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