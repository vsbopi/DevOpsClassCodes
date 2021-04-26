#!/usr/bin/env groovy

node {
    stage('Git Checkout'){
        // code execution
        git 'https://github.com/vsbopi/DevOpsClassCodes.git'
    }
    stage('Addressbook Compile'){
        // code execution
        withMaven(maven:'JenkinsMaven'){
            sh 'mvn compile'
        }
    }
    stage('Addressbook Review'){
        // code execution
        withMaven(maven:'JenkinsMaven'){
            sh 'mvn pmd:pmd'
        }
    }
    stage ('Publish Static Analysis Reports using PMD'){
        //These settings are called out separately and not included in the preceding stage as it will create a concurrency issue
        recordIssues(tools: [pmdParser(pattern: 'target/pmd.xml')])  
    }
    stage('Test'){
        // code execution
        withMaven(maven:'JenkinsMaven'){
            sh 'mvn test'
        }
    }
    stage ('Publish Test Reports using Surefire'){
        //These settings are called out separately and not included in the preceding stage as it will create a concurrency issue
        junit 'target/surefire-reports/*.xml'
    }
    stage('Coverage Check'){
        // code execution
        withMaven(maven:'JenkinsMaven'){
            sh 'mvn cobertura:cobertura -Dcobertura.report.format=xml'
        }
    }
    stage ('Publish Coverage Report using Cobertura'){
        //These settings are called out separately and not included in the preceding stage as it will create a concurrency issue
        cobertura autoUpdateHealth: false, autoUpdateStability: false, coberturaReportFile: 'target/site/cobertura/coverage.xml', conditionalCoverageTargets: '70, 0, 0', failUnhealthy: false, failUnstable: false, lineCoverageTargets: '80, 0, 0', maxNumberOfBuilds: 0, methodCoverageTargets: '80, 0, 0', onlyStable: false, sourceEncoding: 'ASCII', zoomCoverageChart: false
    }
    stage('Package'){
        // code execution
        withMaven(maven:'JenkinsMaven'){
            sh 'mvn package'
        }
    }
    stage('Build Docker Image'){
        // code execution
        steps {
            sh 'cp target/addressbook.war .'
            sh 'docker build . -t vsbopi/addressbook:$BUILD_NUMBER'
            sh 'sudo docker push vsbopi/addressbook:$BUILD_NUMBER'
        }
        
    }
}
