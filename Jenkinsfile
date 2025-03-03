podTemplate(label: 'docker-build', 
  containers: [
    containerTemplate(
      name: 'git',
      image: 'alpine/git',
      command: 'cat',
      ttyEnabled: true
    ),
    containerTemplate(
      name: 'docker',
      image: 'docker',
      command: 'cat',
      ttyEnabled: true
    ),
  ],
  volumes: [ 
    hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'), 
  ]
) {
    node('docker-build') { 
        def appImage
        
        stage('Checkout'){
            container('git'){
                checkout scm
            }
        }
        
        stage('Build'){
            container('docker'){
                script {
                    appImage = docker.build("979050235289.dkr.ecr.ap-southeast-2.amazonaws.com/node-hello-world")
                }
            }
        }

        stage('Push'){
            container('docker'){
                script {
                    docker.withRegistry("https://979050235289.dkr.ecr.ap-southeast-2.amazonaws.com/", "ecr:ap-southeast-2:ecr-cred"){
                        appImage.push("${env.BUILD_NUMBER}")
                        appImage.push("latest")
                    }
                }
            }
        }
      
        stage('Deploy'){
            container('git'){
		kubernetesDeploy(kubeconfigId: 'kube-cred', // REQUIRED
	                 configs: '**/kubernetes/*.yaml',   // REQUIRED
	                 enableConfigSubstitution: true,
	            )
            }
         }
    }    
}
