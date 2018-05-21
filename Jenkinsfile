node('master') {
    stage('checkout') {
        checkout scm
    }

    stage('build') {
        sh 'composer install'
    }

    stage('test') {
      sh './vendor/bin/phpunit';
    }

    stage('create-env-keys') {
        sh 'cp .env.example .env';
        sh 'php artisan key:generate';
    }
}
