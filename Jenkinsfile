podTemplate(label: 'jenkins-slave', 
  containers: [
    containerTemplate(
      name: 'jnlp',
      image: 'jenkinsci/jnlp-slave:3.10-1-alpine',
      args: '${computer.jnlpmac} ${computer.name}'
    ),
    containerTemplate(
      name: 'maven',
      image: 'satishrawat/jenkins:8',
      command: 'cat',
      ttyEnabled: true
    ),
  ],
  volumes: [ 
    hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'), 
  ]
)
{
  node ('jenkins-slave') {
   container('maven') {

parameters {
        string (defaultValue: "10", description: 'Deployment Version', name: 'version')
        choice choices: ['prod', 'dev', 'stag'], description: 'deploy environment', name: 'environment'
    }
stages {
        stage ('checkout code') {
            steps {
                git branch: 'release',
                    credentialsId: 'githublogin',
                    url: 'https://github.com/techmartguru/kube-pipeline'
                          
            
        }               
    }
    stage ('Connect Kubernetes Cluster ') {
            steps {
            sh 'gcloud auth activate-service-account --key-file kubernetes-project-219502-5da68b5b3690.json'
            sh 'gcloud -q beta container clusters get-credentials ha-cluster-1 --region us-central1 --project kubernetes-project-219502'
            }
                
        }
        stage ('Deploy the App ') {
            steps {
            sh 'sed -i s/hello-docker:10/hello-docker:${version}/g test-app.yaml'
            sh 'kubectl apply -f test-app.yaml -n ${environment}'
            
            }
                
        }
    }
    
    
}
