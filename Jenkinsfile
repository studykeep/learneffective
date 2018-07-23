#!groovy
// Jenkinsfile for the Compiling, Verifying, Testing and Deploying the StudyKeep web application.


pipeline {
    agent {
        dockerfile true
    }
    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: 'serverside']], submoduleCfg: [], userRemoteConfigs: [[url: 'studykeep-server.git']]])
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: 'clientside']], submoduleCfg: [], userRemoteConfigs: [[url: 'studykeep-client.git']]])
                sh('. serverside/build.sh')
                sh('. clientside/build.sh')
            }
        }
        stage('Compile') {
            steps {
                sh '''
                	cd serverside &&
                	sh "${tool name: 'sbt-1.1.6', type: 'org.jvnet.hudson.plugins.SbtPluginBuilder$SbtInstallation'}/bin/sbt clean compile"
                '''
            }
        }
        stage('Test') {
            steps {
                sh '''
                	cd serverside &&
                	sh "${tool name: 'sbt-1.1.6', type: 'org.jvnet.hudson.plugins.SbtPluginBuilder$SbtInstallation'}/bin/sbt test"
                '''
            }
        }
        stage('Package') {
            steps {
                sh '''
                	cd build &&
                	sh "${tool name: 'sbt-1.1.6', type: 'org.jvnet.hudson.plugins.SbtPluginBuilder$SbtInstallation'}/bin/sbt package"
                '''
								archiveArtifacts artifacts: 'local/**', fingerprint: true, onlyIfSuccessful: true
            }
        }
        stage('Deploy') {
            steps {
                sh '''
                	cd target/studykeep &&
                	sh "${tool name: 'sbt-1.1.6', type: 'org.jvnet.hudson.plugins.SbtPluginBuilder$SbtInstallation'}/bin/sbt deploy"
                '''
								archiveArtifacts artifacts: 'local/**', onlyIfSuccessful: true
            }
        }
    }
}
