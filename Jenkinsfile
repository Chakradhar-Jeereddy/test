pipeline{
    agent{
        node{
            label "agent1"
        }
    }
    environment{
        course = "devops"
        url = "406682759639.dkr.ecr.us-east-1.amazonaws.com"
        region = "us-east-1"
        project = "roboshop"
        component = "catalogue"
    }
    options{
        disableConcurrentBuilds()
        timeout(time: 10, unit: 'MINUTES')
    }
    stages{
        stage('read version'){
            steps{
                script{
                    // Read and parse package.json file
                     def packageJson = readJSON file: 'package.json'
                     appVersion = packageJson.version
                }
            }
        }
        stage('gather dependencies'){
            steps{
                sh """
                  npm install
                """
            }
        }
        stage('unit test'){
            steps{
                sh """
                  npm test
                """
            }
        }
        stage('sonar scanning'){
            environment{
              def  SCANNER_HOME = tool 'sonar-8.0'
            }
            steps{
                    withSonarQubeEnv('sonar-server'){
                      sh "${SCANNER_HOME}/bin/sonar-scanner"
                    }
            }
        }
        stage('quality gate'){
            steps{
                script{
                    timeout(time: 10, unit: 'MINUTES'){
                        waitForQualityGate abortPipeline: true
                    }
                }
            }
        }
        stage('build image'){
            steps{
                withAWS(credentials: 'aws-auth', region: 'us-east-1') {
                    sh """
                     aws ecr get-login-password --region ${region} | docker login --username AWS --password-stdin ${url}
                     docker build -t ${project}/${component}:latest ${url}/${project}/${component}:${appVersion} .
                     docker images
                    """
                }
            }
        }
    }
}