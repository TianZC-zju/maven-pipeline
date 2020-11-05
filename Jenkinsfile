#!/usr/bin/env groovy

pipeline {
    agent any

    tools {
        maven 'maven305'
        jdk 'jdk8u202'
    }

    stages {
        stage('Build') {
            steps {
                sh "mvn clean install"
                sh "mvn  test package spring-boot:repackage"
                withSonarQubeEnv('SQ2'){
                sh """
                 mvn  org.sonarsource.scanner.maven:sonar-maven-plugin:3.4.1.1168:sonar \
                 -Dsonar.host.url=${SONAR_HOST_URL} \
                 -Dsonar.login=${SONAR_AUTH_TOKEN} \
                 -Dsonar.java.binaries=target/classes\
                 -Dsonar.sources=src/main/java\
                 -Dsonar.tests=src/test/java\
                 -Dsonar.junit.reportsPath=target/surefire-reports\
                 -Dsonar.java.coveragePlugin=jacoco\
                 -Dsonar.jacoco.reportPath=target/coverage-reports/jacoco-unit.exec
                 
                 """
                }
            }
        }

    }
    post {
        always{
//            junit 'target/surefire-reports/**/*.xml'exclusionPattern: 'src/test*',
            junit(testResults: '**/target/surefire-reports/**/*.xml')
            jacoco(
                    execPattern: 'target/**/*.exec',
                    classPattern: 'target/classes',
                    sourcePattern: 'src/main/java',
                    
                    buildOverBuild: true,
                    changeBuildStatus: true,
                    deltaLineCoverage: '69',
                    minimumLineCoverage:'0',
                    maximumLineCoverage: '61'
            )

        }

    }
}
