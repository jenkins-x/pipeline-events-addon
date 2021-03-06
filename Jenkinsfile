pipeline {
    environment {
        CHARTMUSEUM_CREDS = credentials('jenkins-x-chartmuseum')
    }
    agent any
    stages {
        stage('CI Build') {
            when {
                branch 'PR-*'
            }
            steps {
                dir ('/home/jenkins/pipeline-events-addon') {
                    checkout scm
                    sh "helm init --client-only"

                    sh "make build"
                    sh "helm template ."
                }
            }
        }
    
        stage('Build and Push Release') {
            when {
                branch 'master'
            }
            steps {
                dir ('/home/jenkins/pipeline-events-addon') {
                    git "https://github.com/jenkins-x/pipeline-events-addon"

                    sh "git checkout master"

                    sh "git config credential.helper store"
                    sh "jx step git credentials"
                    sh "helm init --client-only"
                    sh "helm repo add incubator https://kubernetes-charts-incubator.storage.googleapis.com"
                    sh "helm repo add stable https://kubernetes-charts.storage.googleapis.com"
                    sh "helm repo add jx http://chartmuseum.build.cd.jenkins-x.io"
                    sh "make release"
                }
            }
        }
    }
}
