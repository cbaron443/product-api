pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building..'
                bat 'mvn -B -U -e -V clean -DskipTests package'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
                 bat 'mvn -U -V -e -B -DskipTests -PDEV -DDEV deploy -DmuleDeploy'
            }
        }
    }
}