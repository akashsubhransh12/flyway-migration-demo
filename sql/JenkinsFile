pipeline {
    agent any

    parameters {
        booleanParam(name: 'RUN_PROD', defaultValue: false, description: 'Run Flyway on PROD after QA?')
    }

    environment {
        QA_DB_URL = 'jdbc:postgresql://localhost:5433/qa_db'
        PROD_DB_URL = 'jdbc:postgresql://localhost:5434/prod_db'
    }

    stages {

        stage('Checkout Repo') {
            steps {
                checkout scm
            }
        }

        stage('Flyway Validate') {
            steps {
                echo "Validating migrations..."
                sh '''
                docker run --rm \
                    -v $PWD/sql:/flyway/sql \
                    flyway/flyway:latest \
                    -url=${QA_DB_URL} \
                    -user=admin \
                    -password=admin \
                    validate
                '''
            }
        }

        stage('Run Migration on QA DB') {
            steps {
                echo "Running migration on QA database..."
                sh '''
                docker run --rm \
                    -v $PWD/sql:/flyway/sql \
                    flyway/flyway:latest \
                    -url=${QA_DB_URL} \
                    -user=admin \
                    -password=admin \
                    migrate
                '''
            }
        }

        stage('Approval for PROD') {
            when {
                expression { return params.RUN_PROD }
            }
            steps {
                input message: "QA Successful. Approve migration to PROD?"
            }
        }

        stage('Run Migration on PROD DB') {
            when {
                expression { return params.RUN_PROD }
            }
            steps {
                echo "Running migration on PROD database..."
                sh '''
                docker run --rm \
                    -v $PWD/sql:/flyway/sql \
                    flyway/flyway:latest \
                    -url=${PROD_DB_URL} \
                    -user=admin \
                    -password=admin \
                    migrate
                '''
            }
        }
    }
}
