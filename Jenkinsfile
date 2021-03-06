node('master') {
    stage('checkout') {
        checkout scm
    }

    stage('build') {
        sh 'composer install'
    }

    stage('create-env-keys') {
        sh 'cp .env.example .env';
        sh 'php artisan key:generate';
    }

    stage('test') {
        sh './vendor/bin/phpunit --log-junit reports/xunit --coverage-clover reports/coverage';
    }

    stage('xunit') {
        junit 'reports/xunit'
    }

    stage('coverage') {
        step([
                $class: 'CloverPublisher',
                cloverReportDir: 'reports',
                cloverReportFileName: 'coverage',
                healthyTarget: [methodCoverage: 10, conditionalCoverage: 10, statementCoverage: 10],
                unhealthyTarget: [methodCoverage: 5, conditionalCoverage: 5, statementCoverage: 5],
                failingTarget: [methodCoverage: 0, conditionalCoverage: 0, statementCoverage: 0]
        ])
    }

    stage('confirm-deploy') {
        input message: "Deploy this build?"
    }

    stage('build-deb') {
        sh "fpm -s dir -t deb -n michaeltest -v '1.0.${env.BUILD_NUMBER}' .=/var/www/michaeltest"
    }

    stage('deploy') {
        withCredentials([[$class: 'FileBinding', credentialsId: 'ssh-key', variable: 'SECRET_FILE']]) {
            dir('ansible') {
                sh "ansible-playbook -i inv --key-file=$SECRET_FILE playbook.yml --extra-vars '{\"workspace\": \"${env.WORKSPACE}\"}'"
            }
        }
    }

}
