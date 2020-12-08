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
    image: localhost:5000/stockmanager:latest
    imagePullPolicy: ""
    ports:
    - containerPort: 8030
      protocol: TCP
    resources: {}
  - name: productcatalogue  
    image: localhost:5000/productcatalogue:latest
    imagePullPolicy: ""
    ports:
    - containerPort: 8020
      protocol: TCP
    resources: {} 
  - name: stockmanager  
    image: localhost:5000/shopfront:latest
    imagePullPolicy: ""
    ports:
    - name: http
      containerPort: 8010
      protocol: TCP
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