import groovy.json.JsonSlurper

pipeline {
    agent any

    environment {
        PORT_CLIENT_ID = "sofrHu9n5JYBHEtYYzxERT7wnxF3ZxCG"
        PORT_CLIENT_SECRET = "2imgxDc9KCyxkRCZfOaVUhHWKiJvUe4yP3pGk0wEURpfrEMShup2MY3R2ud0AdaT"
        ACCESS_TOKEN = "Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJvcmdJZCI6Im9yZ19WNURZWFhjajRkWDdLdzRSIiwiaXNzIjoiaHR0cHM6Ly9hcGkuZ2V0cG9ydC5pbyIsImlzTWFjaGluZSI6dHJ1ZSwic3ViIjoic29mckh1OW41SllCSEV0WVl6eEVSVDd3bnhGM1p4Q0ciLCJqdGkiOiI1OGYzMmE5MS00ZTljLTRkZjUtOWVlMS1kMGM0NTZhNWI5ZWMiLCJpYXQiOjE3MTgyNjk5OTYsImV4cCI6MTcxODI4MDc5Nn0.2MAdX44eYgI1k5-yMVCKFi09VSlwarIP-ltSf7ALWOM"
        RUN_ID = ${{ fromJson(inputs.port_context).run_Id }} // Set your RUN_ID here or pass it as a parameter
    }

    stages {
        stage('Hello') {
            steps {
                echo 'Hello World'
            }
        }

        stage('Get access token') {
            steps {
                script {
                    // Execute the curl command and capture the output
                    def result = sh(returnStdout: true, script: """
                        accessTokenPayload=\$(curl -X POST \
                            -H "Content-Type: application/json" \
                            -d '{"clientId": "${PORT_CLIENT_ID}", "clientSecret": "${PORT_CLIENT_SECRET}"}' \
                            -s "https://api.getport.io/v1/auth/access_token")
                        echo \$accessTokenPayload
                    """)

                    // Parse the JSON response using JsonSlurper
                    def jsonSlurper = new JsonSlurper()
                    def payloadJson = jsonSlurper.parseText(result.trim())

                    // Access the desired data from the payload
                    ACCESS_TOKEN = payloadJson.accessToken
                    env.ACCESS_TOKEN = ACCESS_TOKEN // Export to environment for subsequent stages
                }
            }
        }

        stage('Send logs example') {
            steps {
                script {
                    def logs_report_response = sh(script: """
                        curl -X POST \
                            -H "Content-Type: application/json" \
                            -H "Authorization: Bearer ${ACCESS_TOKEN}" \
                            -d '{"message": "this is a log test message example"}' \
                            "https://api.getport.io/v1/actions/runs/${RUN_ID}/logs"
                    """, returnStdout: true)

                    println(logs_report_response)
                }
            }
        }

        stage('Update status example') {
            steps {
                script {
                    def status_report_response = sh(script: """
                        curl -X PATCH \
                          -H "Content-Type: application/json" \
                          -H "Authorization: Bearer ${ACCESS_TOKEN}" \
                          -d '{"status":"SUCCESS", "message": {"run_status": "Jenkins CI/CD Run completed successfully!"}}' \
                          "https://api.getport.io/v1/actions/runs/${RUN_ID}"
                    """, returnStdout: true)

                    println(status_report_response)
                }
            }
        }
    }
}
