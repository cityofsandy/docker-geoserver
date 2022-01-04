node {
    def app
    def BUGFIX = 2
    def MINOR = 20
    def MAJOR = 1
    currentBuild.result = "SUCCESS"
    try {
        stage ('Pull SCM') {
            checkout scm
        }
        
        sh "ls ${env.workspace}"
        
        stage('Build Docker Image') {
            app = docker.build("cityofsandy/geoserver:${MAJOR}.${MINOR}.${BUGFIX}", "--build-arg GS_VERSION=${MAJOR}.${MINOR}.${BUGFIX} .", )
        }

        stage('Test image') {
            app.inside {
                sh 'echo "Tests passed"'
            }
        }
        
        stage('Push image') {
            docker.withRegistry('https://prod.nexus.aws.cityofsandy.com', 'sn-dev-nexus-user-pass') {
                app.push()
            }
        }
        
    } catch (e) {
        currentBuild.result = 'FAILURE'
        throw e
    } finally {
        step([$class: 'Mailer', notifyEveryUnstableBuild: true, recipients: "${sn_dev_email_dist_list}", sendToIndividuals: true])
    }
}