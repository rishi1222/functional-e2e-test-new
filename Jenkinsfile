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
    image: localhost:5000/maven:latest
    command:
    - cat
    tty: true
  - name: shopfront  
    image: localhost:5000/shopfront:latest
    imagePullPolicy: ""
    ports:
    - protocol: TCP
      containerPort: 8010
      targetPort: http
      hostPort: 8010
    resources: {}      
  - name: stockmanager  
    image: localhost:5000/stockmanager:latest
    imagePullPolicy: ""
    ports:
    - protocol: TCP
      containerPort: 8030
      targetPort: http
      hostPort: 8030
    resources: {}
  - name: productcatalogue  
    image: localhost:5000/productcatalogue:latest
    imagePullPolicy: ""
    ports:
    - protocol: TCP
      containerPort: 8020
      targetPort: http
      hostPort: 8020
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