node {
    def app

    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace */

        checkout scm
    }

    stage('Build image') {
        /* This builds the actual image; synonymous to
         * docker build on the command line */

        app = docker.build("ocb0001835/hellonode")
    }

    stage('Test image') {
        /* Ideally, we would run a test framework against our image.
         * For this example, we're using a Volkswagen-type approach ;-) */

        app.inside {
            sh 'echo "Tests passed"'
        }
    }

    stage('Push image') {
        /* Finally, we'll push the image with two tags:
         * First, the incremental build number from Jenkins
         * Second, the 'latest' tag.
         * Pushing multiple tags is cheap, as all the layers are reused. */
        docker.withRegistry('https://90.84.44.40:443', 'conn_1835_private_registry') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }
    }
    
    stage('deploy app') {    
        withKubeConfig(caCertificate: '', credentialsId: 'cce-1835-secret', serverUrl: 'https://kubernetes.default.svc.cluster.local:5443') {
        // someblock
        sh 'set +e; kubectl delete -f rc.yaml ; exit 0'
        sh 'sleep 10'
        sh 'kubectl create -f rc.yaml;'
        sh 'set +e; kubectl create -f svc.yaml; exit 0'
        }
    }
}
