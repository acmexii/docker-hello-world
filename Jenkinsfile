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
        
        stage('Test'){
            container('docker'){
                script {
                    appImage.inside {
                        sh 'npm install'
                        sh 'npm test'
                    }
                }
            }
        }

        stage('Push'){
            container('docker'){
                script {
                    docker.withRegistry('https://052937454741.dkr.ecr.eu-central-1.amazonaws.com/', 'ecr:eu-central-1:ecr-cred'){
                        appImage = docker.build("052937454741.dkr.ecr.eu-central-1.amazonaws.com/node-hello-world")
                        appImage.push("${env.BUILD_NUMBER}")
                        appImage.push("latest")
                    }
                }
            }
        }
    }
    
}
