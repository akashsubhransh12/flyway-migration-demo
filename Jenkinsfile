pipeline {
    agent any

    environment {
        FLYWAY_HOME = 'C:\\Program Files\\Red Gate\\Flyway Desktop\\flyway'
        QA_DB_URL   = 'jdbc:mysql://127.0.0.1:3306/qa_db?allowPublicKeyRetrieval=true&useSSL=false'
        PROD_DB_URL = 'jdbc:mysql://127.0.0.1:3306/prod_db?allowPublicKeyRetrieval=true&useSSL=false'

        DB_USER     = 'root'
        DB_PASSWORD = 'Akash@1234'
    }

    stages {

        stage('Checkout Repo') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/akashsubhransh12/flyway-migration-demo.git'

                script {
                    env.SQL_PATH = "${env.WORKSPACE}\\sql"
                }

                bat "dir ${env.SQL_PATH}"
            }
        }

        stage('Migrate QA') {
            steps {
                echo "Running QA Migration..."

                bat "\"${FLYWAY_HOME}\\flyway.cmd\" -url=\"${QA_DB_URL}\" -user=${DB_USER} -password=${DB_PASSWORD} -locations=filesystem:${env.SQL_PATH} migrate"

                echo "QA Migration Info:"
                bat "\"${FLYWAY_HOME}\\flyway.cmd\" -url=\"${QA_DB_URL}\" -user=${DB_USER} -password=${DB_PASSWORD} -locations=filesystem:${env.SQL_PATH} info"
            }
        }

        stage('Approval for PROD') {
            steps {
                input message: 'Approve PROD migration?', ok: 'Deploy to PROD'
            }
        }

        stage('Migrate PROD') {
            steps {
                echo "Running PROD Migration..."

                bat "\"${FLYWAY_HOME}\\flyway.cmd\" -url=\"${PROD_DB_URL}\" -user=${DB_USER} -password=${DB_PASSWORD} -locations=filesystem:${env.SQL_PATH} migrate"

                echo "PROD Migration Info:"
                bat "\"${FLYWAY_HOME}\\flyway.cmd\" -url=\"${PROD_DB_URL}\" -user=${DB_USER} -password=${DB_PASSWORD} -locations=filesystem:${env.SQL_PATH} info"
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed! Check logs."
        }
    }
}
