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
                    appImage = docker.build("283210891307.dkr.ecr.ap-northeast-2.amazonaws.com/node-hello-world")
                }
            }
        }

        stage('Push'){
            container('docker'){
                script {
                    docker.withRegistry("https://283210891307.dkr.ecr.ap-northeast-2.amazonaws.com/", ""){
                        appImage.push("${env.BUILD_NUMBER}")
                        appImage.push("latest")
                    }
                }
            }
        }
      
        stage('Deploy'){
            container('git'){
		kubernetesDeploy(// kubeconfigId: 'kube-cred', 
	                 configs: '**/kubernetes/*.yaml',   // REQUIRED
	                 enableConfigSubstitution: true,
	            )
            }
         }
    }    
}
