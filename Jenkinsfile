#!/usr/bin/groovy

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
        string(name: 'gitbranch', defaultValue: 'master', description: 'Git Branch')
        choice choices: ['prod', 'dev' ], description: 'Deploy environment', name: 'Environment'
    }

    stage('Clone Repo') { 
    git branch: 'master',
                    
                    url: 'https://github.com/techmartguru/kube-pipeline'
      
     stage('Maven Package') {
                    sh ("mvn clean package -DskipTests")
                }
        
    }
    
    stage ('Testing Stage') {
            
                sh 'echo "Hi This is testing"'
                
            
    }
    
    stage ('Create Docker Image and push ') {
            
            sh 'cat kube-cluster-231707-6b9613e3dd78.json | docker login -u _json_key --password-stdin https://gcr.io'
          sh 'docker build -t gcr.io/kube-cluster-231707/hello-docker:${BUILD_NUMBER} .'
                    sh 'docker push gcr.io/kube-cluster-231707/hello-docker:${BUILD_NUMBER}'
            
        }
        stage ('Connect Kubernetes Cluster ') {
            
            sh 'gcloud auth activate-service-account --key-file kube-cluster-231707-6b9613e3dd78.json'
            sh 'gcloud container clusters get-credentials kube-poc --zone us-central1-a --project kube-cluster-231707'
            
                
        }
        stage ('Deploy the App ') {
            
            sh 'sed -i s/hello-docker:10/hello-docker:${BUILD_NUMBER}/g test-app.yaml'
            sh 'kubectl apply -f test-app.yaml -n dev'
            
            } 
}
}
}
