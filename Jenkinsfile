#!groovy

pipeline {
	agent any

	stages {
		stage('growing the podlings') {

			podTemplate(cloud: 'k5', label: 'mypod', containers: [
				containerTemplate(name: 'maven', image: 'maven:3.3.9-jdk-8-alpine', ttyEnabled: true, command: 'cat'),
				containerTemplate(name: 'golang', image: 'golang:1.6.3-alpine', ttyEnabled: true, command: 'cat')
			  ],
			  volumes: [secretVolume(secretName: 'shared-secrets', mountPath: '/etc/shared-secrets')]) {

				node ('mypod') {
					stage('Get a Maven project') {
						git 'https://github.com/jenkinsci/kubernetes-plugin.git'
					}
					container('maven') {
						stage('Build a Maven project') {
							sh 'mvn clean install'
						}
					}

					stage('Get a Golang project') {
						git url: 'https://github.com/hashicorp/terraform.git'
					}

					container('golang') {
						stage('Build a Go project') {
							sh """
							mkdir -p /go/src/github.com/hashicorp
							ln -s `pwd` /go/src/github.com/hashicorp/terraform
							cd /go/src/github.com/hashicorp/terraform && make core-dev
							"""
						}
					}

				}
			}
		}
	}
}


