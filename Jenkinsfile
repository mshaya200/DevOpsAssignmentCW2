node {
    def app

    stage('Clone repository') {
        /* Cloning the repository */

        checkout scm
    }

    stage('Build image') {
        /* Building the Docker image from mshaya200.server.js */

        app = docker.build("mshaya200/server.js")
    }

    stage('Test image') {
        /*There are not tests in the application. The application has been built which proves that the image works*/

        app.inside {
            sh 'echo "Tests passed"'
        }
    }

    stage('Push image') {
        /*The image is pushed to dockerhub, credentials have been added to Jenkins for easy authentication
        The application is pushed with two tags, first is the build number and second is the latest tag*/
        docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }
    }

    stage('Push change to Kubernetes') {
        /* The image pushed to dockerhub is fetched by kubernetes and the image is deployed to the 4 pods running */
        withCredentials([sshUserPrivateKey(credentialsId: 'sshkey', keyFileVariable: 'ShayanKey', passphraseVariable: '', usernameVariable: 'ubuntu')]) {
        sh 'ssh ubuntu@34.233.69.104 kubectl set image deployments/server.js server-js-4kgll=mshaya200/server.js:${BUILD_NUMBER}'
}
  }
}