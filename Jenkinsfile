pipeline {
    environment {
        GH_CREDS = credentials('jenkins-x-github')
        CHARTMUSEUM_CREDS = credentials('jenkins-x-chartmuseum')
    }
    agent {
        label "jenkins-jx-base"
    }
    stages {
        stage('CI Build') {
            when {
                branch 'PR-*'
            }
            steps {
                dir ('/home/jenkins/pipeline-events-addon') {
                    checkout scm
                    container('jx-base') {
                        sh "helm init --client-only"

                        sh "make build"
                        sh "helm template ."
                    }
                }
            }
        }
    
        stage('Build and Push Release') {
            when {
                branch 'master'
            }
            steps {
                dir ('/home/jenkins/pipeline-events-addon') {
                    checkout scm
                    container('jx-base') {

                        sh "git checkout master"

                        sh "git config credential.helper store"

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
}