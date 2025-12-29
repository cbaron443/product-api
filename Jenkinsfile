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
        stage('MUnit Test') {
            steps {
                echo 'Executing MUnit Test Cases...'
                configFileProvider([configFile(fileId: 'mule-maven-setting-from-m2localxml', variable: 'MVN_SETTINGS')]) {
                    // Use 'mvn test' to trigger MUnit execution exclusively
                    bat 'mvn test -s %MVN_SETTINGS%'
                }
            }
            post {
                always {
                    // Automatically publish test results to the Jenkins UI
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }
        
        stage('Publish to Local Nexus') {
            steps {
                echo 'Archiving to Local Nexus...'
                configFileProvider([configFile(fileId: 'mule-maven-setting-from-m2localxml', variable: 'MVN_SETTINGS')]) {
                    bat 'mvn deploy -DskipTests -s %MVN_SETTINGS% -DaltDeploymentRepository=local-nexus-snapshots::default::http://your-nexus-ip:8081/repository/maven-snapshots/'
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