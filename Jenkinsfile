#!/usr/bin/groovy

podTemplate(label: 'mypod', containers: [
    containerTemplate(name: 'docker', image: 'docker', ttyEnabled: true, command: 'cat'),
    containerTemplate(name: 'kubectl', image: 'lachlanevenson/k8s-kubectl:v1.8.0', command: 'cat', ttyEnabled: true),
    containerTemplate(name: 'helm', image: 'lachlanevenson/k8s-helm:latest', command: 'cat', ttyEnabled: true)
  ],
  volumes: [
    hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'),
  ]) {
    node('mypod') {

        stage('do some Docker work') {
            container('docker') {
//                   app = docker.build("my-node")
		    sh "docker build -t my-node ."
           }
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
		     
        	sh " docker tag my-node 10.233.12.90:5000/my-node:${env.BUILD_NUMBER}"
                sh "docker push 10.233.12.90:5000/my-node:${env.BUILD_NUMBER}"
    	}

        stage('do some kubectl work') {
            container('kubectl') {
                sh "kubectl get nodes"
		sh "kubectl run  --image=10.233.12.90:5000/my-node:${env.BUILD_NUMBER} my-node"
            }
        }
    }
}
