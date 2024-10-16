pipeline {
    agent none

    stages {
        stage('Build') {
            agent {
                kubernetes {
                    label 'python-builder'
                    defaultContainer 'python'
                    yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: python
    image: python:alpine3.17
    command:
    - cat
    tty: true
    securityContext:
      runAsUser: 0  # Run container as root
"""
                }
            }
            steps {
                echo 'Compiling vote app'
                sh 'pip install -r requirements.txt'
            }
        }

        stage('Unit Test') {
            agent {
                kubernetes {
                    label 'python-test'
                    defaultContainer 'python'
                    yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: python
    image: python:alpine3.17
    command:
    - cat
    tty: true
    securityContext:
      runAsUser: 0  # Run container as root
"""
                }
            }
            steps {
                echo 'Running Unit Tests on vote app'
                sh 'pip install -r requirements.txt'
                echo 'Placeholder to run nosetests -v'
            }
        }

        stage('Docker Build and Push with Kaniko') {
            agent {
                kubernetes {
                    label 'kaniko-agent'
                    defaultContainer 'kaniko'
                    yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: kaniko
    image: gcr.io/kaniko-project/executor:latest
    args: ["--dockerfile=Dockerfile", "--context=dir://workspace/", "--destination=docker.io/xxxxxx/vote:\${BUILD_ID}", "--destination=docker.io/xxxxxx/vote:dev"]
    volumeMounts:
      - name: docker-config
        mountPath: /kaniko/.docker
  volumes:
  - name: docker-config
    secret:
      secretName: docker-registry-credentials
"""
                }
            }
            when {
                branch "master"
            }
            steps {
                echo 'Packaging vote app with Kaniko'
            }
        }
    }

    post {
        always {
            echo 'Pipeline for vote is complete..'
        }
    }
}
