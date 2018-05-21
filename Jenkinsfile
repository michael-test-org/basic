node('master') {
    stage('checkout') {
        checkout scm
    }

    stage('build') {
        sh 'composer install'
    }
}
