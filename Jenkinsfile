#!/usr/bin/env groovy
pipeline {
    agent {
        kubernetes {
            yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: jenkins
spec:
  containers:
  - name: maven
    image: localhost:5000/my-maven:latest
    command:
    - cat
    tty: true
  - name: shopfront  
    image: localhost:5000/shopfront:latest
    command: ['sh', '-c', 'echo The app is running! && sleep 3600']
    imagePullPolicy: ""
    ports:
    - protocol: TCP
      containerPort: 8010
      targetPort: http
    resources: {} 
  initContainers:      
  - name: stockmanager  
    image: localhost:5000/stockmanager:latest
    command: ['sh', '-c', 'until nslookup stockmanager; do echo waiting for stockmanager; sleep 2; done;']
    imagePullPolicy: ""
    ports:
    - protocol: TCP
      containerPort: 8030
      targetPort: http
    resources: {}
  - name: productcatalogue  
    image: localhost:5000/productcatalogue:latest
    command: ['sh', '-c', 'until nslookup productcatalogue; do echo waiting for productcatalogue; sleep 2; done;']
    imagePullPolicy: ""
    ports:
    - protocol: TCP
      containerPort: 8020
      targetPort: http
    resources: {}        
  restartPolicy: Always
  serviceAccountName:     
  imagePullSecrets:
  - name: regcred    
"""
        }
    }
    stages {
        stage('Run maven') {
            steps {
                container('maven') {
                    sh 'mvn -version'
                }
            }
        }
        stage('e2e test') {
            steps {
                container('maven') {
                    sh 'mvn test'
                }
            }
        }
    }
}