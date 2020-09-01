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
