def COLOR_MAP = [
    'SUCCESS': 'good',
    'FAILURE': 'danger',
]
//def getBuildUser() {
//    return currentBuild.rawBuild.getCause(Cause.UserIdCause).getUserId()
//}

pipeline {
    agent
    {
        node {
            label params.NODE
        }
     }
    stages {
        stage('Build Lib') {
                steps {
                sh '''#!/bin/bash
                    lein install
                        if [ $? -gt 0 ]; then
                            echo "Build Failure."
                            exit 1
                        fi
                    cd ..
                '''
            }
        }
        stage('Publish') {

            steps {

                withCredentials([usernamePassword(credentialsId: "nexus-$repository", usernameVariable: 'USER', passwordVariable: 'PASSWORD')]) {
                script {

                sh '''#!/bin/bash
                    export LEIN_USERNAME=$USER
                    export LEIN_PASSWORD=$PASSWORD

                        if [[ $repository == snapshots ]]; then
                            lein deploy snapshots
                        elif [[ $repository == releases ]]; then
                            lein deploy releases
                        else
                            echo "repo not specified"
                            exit 1
                        fi
                        cd ..
                '''
                }

            }
        }
    }
    }
    post {
        always {
            //script {
                //BUILD_USER = getBuildUser()
            //}
            slackSend channel: '#jenkins-build',
                color: COLOR_MAP[currentBuild.currentResult],
                message: "*${currentBuild.currentResult}:* CLJ-Libs $repository $ENV \n Job ${env.JOB_NAME}  $GIT_BRANCH build number ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
        }
    }

}
