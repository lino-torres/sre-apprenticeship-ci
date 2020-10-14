#!/usr/bin/env groovy

pipeline {
 agent none
 environment{
     YOUR_DOCKERHUB_USER = "linuxtorres"
     DOCKER_HUB = credentials('linuxtorres-dockerhub-credentials')
 }

 stages {
   stage('Unit test frontend') {
     agent any

     steps {
         sh "docker build -t frontend-tests -f Frontend/Dockerfile.unit-test ./Frontend"
         sh "docker run frontend-tests"
     }
   }
   stage('Build frontend') {
     agent any
     when {
       branch 'main'
     }
     steps {
         sh "docker build -t $YOUR_DOCKERHUB_USER/todo-frontend:${GIT_COMMIT} -f Frontend/Dockerfile ./Frontend"
         sh "docker login -u $DOCKER_HUB_USR -p $DOCKER_HUB_PSW"
         sh "docker push $YOUR_DOCKERHUB_USER/todo-frontend:${GIT_COMMIT}"
     }
   }
   stage('Unit test backend') {
     agent any

     steps {
         sh "docker build -t backend-tests -f Backend/Dockerfile.unit-test ./Backend"
     }
   }
   stage('Build backend') {
     agent any
     when {
       branch 'main'
     }
     steps {
         sh "docker build -t $YOUR_DOCKERHUB_USER/todo-backend:${GIT_COMMIT} -f Backend/Dockerfile ./Backend"
         sh "docker login -u $DOCKER_HUB_USR -p $DOCKER_HUB_PSW"
         sh "docker push $YOUR_DOCKERHUB_USER/todo-backend:${GIT_COMMIT}"
     }
   }
   stage('Cleanup tests'){
     agent any
     steps {
       sh "docker rmi -f frontend-tests"
       sh "docker rmi -f backend-tests"
     }
   }
   stage('Cleanup images'){
     agent any
     when {
       branch 'main'
     }
     steps {
       sh "docker rmi -f $YOUR_DOCKERHUB_USER/todo-frontend:${GIT_COMMIT}"
       sh "docker rmi -f $YOUR_DOCKERHUB_USER/todo-backend:${GIT_COMMIT}"
     }
   }
 }
}