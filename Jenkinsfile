pipeline {
  agent {
    kubernetes {
      yaml """
apiVersion: v1
kind: Pod
metadata:
spec:
  containers:
  - name: helm
    image: alpine/helm
    command: 
    - cat
    tty: true
  - name: kubectl
    image: bryandollery/terraform-packer-aws-alpine
    command:
    - cat
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


    stage('deploy:helm') {
            steps {
                container('kubectl') {
                   sh '''
		    kubectl delete  validatingwebhookconfigurations.admissionregistration.k8s.io prometheus-prometheus-oper-admission
                    kubectl delete  MutatingWebhookConfiguration  prometheus-prometheus-oper-admission
                    helm repo add stable https://kubernetes-charts.storage.googleapis.com
                    helm repo update
	            helm install prometheus-operator stable/prometheus-operator --namespace monitor --set grafana.service.type=NodePort --set prometheusOperator.admissionWebhooks.enabled=false --set prometheusOperator.admissionWebhooks.patch.enabled=false --set prometheusOperator.tlsProxy.enabled=false -f toleration.yaml                  
		    kubectl delete  validatingwebhookconfigurations.admissionregistration.k8s.io prometheus-prometheus-oper-admission
                    kubectl delete  MutatingWebhookConfiguration  prometheus-prometheus-oper-admission
		  '''
                          
          }
      }
   }
 }
}
