pipeline{
    agent{
        node{
            label "agent1"
        }
    }
    environment{
        course = "devops"
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
    }
}