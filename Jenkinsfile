pipeline {
  agent {
    kubernetes {
      yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    some-label: some-label-value
spec:
  containers:
  - name: pro-grafa
    image: bryandollery/terraform-packer-aws-alpine 
    command: 
    - bash
    tty: true
"""
    }
  }
	environment {
    TOKEN=credentials('bc33a7c7-818f-47d0-9140-94822190010c')
  }
stages { 
    stage('deploy:kubectl') {
          steps {
              container('kubectl') {
                  sh '''
                       kubectl --token=$TOKEN create namespace monitor    
		      
                  '''
              }
          }
      }
	
  stages {
      stage("build") {
          steps {
              container('pro-grafa') {
                   sh '''
			helm repo add stable https://kubernetes-charts.storage.googleapis.com
			helm repo update
			helm install prometheus-operator stable/prometheus-operator --namespace monitor --set grafana.service.type=NodePort --set prometheusOperator.admissionWebhooks.enabled=false --set prometheusOperator.admissionWebhooks.patch.enabled=false --set prometheusOperator.tlsProxy.enabled=false
                  '''
              }
          }
      }
  }

}
