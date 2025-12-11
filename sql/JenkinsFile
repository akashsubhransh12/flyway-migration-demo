pipeline {
    agent any
    environment {
        // Update these paths according to your server
        FLYWAY_HOME = 'C:\\Program Files\\Red Gate\\Flyway Desktop\\flyway'
        SQL_PATH = 'C:\\Program Files\\Red Gate\\Flyway Desktop\\flyway\\sql' // will be overwritten after git checkout
        QA_DB_URL = 'jdbc:mysql://127.0.0.1:3306/qa_db?allowPublicKeyRetrieval=true&useSSL=false'
        PROD_DB_URL = 'jdbc:mysql://127.0.0.1:3306/prod_db?allowPublicKeyRetrieval=true&useSSL=false'
        DB_USER = 'root'
        DB_PASSWORD = 'Akash@1234'
    }
    stages {
        stage('Checkout SQL scripts') {
            steps {
                // Clone your Git repo with SQL scripts
                git branch: 'main', url: 'https://github.com/akashsubhransh12/flyway-migration-demo.git'

                // Update SQL_PATH to point to the cloned repo folder
                script {
                    SQL_PATH = "${env.WORKSPACE}\\sql"
                }

                bat "dir ${SQL_PATH}"
            }
        }

        stage('Migrate QA') {
            steps {
                echo 'Running Flyway migration on QA database...'
                bat "\"${FLYWAY_HOME}\\flyway.exe\" -url=\"${QA_DB_URL}\" -user=${DB_USER} -password=${DB_PASSWORD} -locations=filesystem:${SQL_PATH} migrate"

                echo 'QA Migration Info:'
                bat "\"${FLYWAY_HOME}\\flyway.exe\" -url=\"${QA_DB_URL}\" -user=${DB_USER} -password=${DB_PASSWORD} -locations=filesystem:${SQL_PATH} info"
            }
        }

        stage('Approval for PROD') {
            steps {
                input message: 'Approve PROD migration?', ok: 'Deploy to PROD'
            }
        }

        stage('Migrate PROD') {
            steps {
                echo 'Running Flyway migration on PROD database...'
                bat "\"${FLYWAY_HOME}\\flyway.exe\" -url=\"${PROD_DB_URL}\" -user=${DB_USER} -password=${DB_PASSWORD} -locations=filesystem:${SQL_PATH} migrate"

                echo 'PROD Migration Info:'
                bat "\"${FLYWAY_HOME}\\flyway.exe\" -url=\"${PROD_DB_URL}\" -user=${DB_USER} -password=${DB_PASSWORD} -locations=filesystem:${SQL_PATH} info"
            }
        }
    }
    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed! Check logs for details.'
        }
    }
}
