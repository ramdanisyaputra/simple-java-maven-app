node {
    docker.image('maven:3.9.0').inside('-v /root/.m2:/root/.m2') {
        stage('Build') {
            try {
                sh 'mvn -B -DskipTests clean package'
            } catch (err) {
                error("Failed in Build stage with error: ${err}")
            }
        }

        stage('Test') {
            try {
                sh 'mvn test'
            } catch (err) {
                error("Failed in Test stage with error: ${err}")
            } finally {
                step([$class: 'JUnitResultArchiver', testResults: 'target/surefire-reports/*.xml'])
            }
        }

        stage('Manual Approval') {
            try {
                input message: 'Lanjutkan ke tahap Deploy?', ok: 'Proceed'
            } catch (err) {
                error("Deployment was not approved: ${err}")
            }
        }

        stage('Deploy') {
            try {
                sh './jenkins/scripts/deliver.sh'
                echo 'Pausing for 1 minute while the app runs...'
                sleep(time: 1, unit: 'MINUTES')
            } catch (err) {
                error("Failed in Deploy stage with error: ${err}")
            }
        }
    }
}