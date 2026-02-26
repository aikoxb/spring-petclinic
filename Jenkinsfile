// Jenkinsfile for Java project with JaCoCo code coverage and artifact generation
pipeline {
    agent any

    // every 5 minutes on Thursday
    triggers {
        cron('H/5 * * * 4')
    }

    tools {
        jdk 'JDK17'
    }

    stages {
        // Stage runs first to check out the code from the repository
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        // Stage runs after the code is checked out from the repository
        // It executes the Gradle task 'clean test' to run the tests and generate the JaCoCo execution data (.exec files)
        stage('Build and Test') {
            steps {
                script {
                    if (isUnix()) {
                        sh 'chmod +x gradlew'
                        sh './gradlew clean test'
                    } else {
                        bat 'gradlew.bat clean test'
                    }
                }
            }
        }

        // Stage runs after the tests have completed 
        // It generates the JaCoCo coverage report using the Gradle task 'jacocoTestReport'
        stage('JaCoCo Coverage Report') {
            steps {
                script {
                    if (isUnix()) {
                        sh './gradlew jacocoTestReport'
                    } else {
                        bat 'gradlew.bat jacocoTestReport'
                    }
                }
            }
            post {
                always {
                    // 'jacoco' step is used to publish the JaCoCo coverage report to Jenkins
                    jacoco(
                        execPattern: '**/build/jacoco/*.exec',
                        classPattern: '**/build/classes/java/main',
                        sourcePattern: '**/src/main/java',
                        inclusionPattern: '**/*.class'
                    )
                }
            }
        }

        // Stage runs after the JaCoCo report is generated
        // It generates the artifact (JAR file) using the Gradle task 'build'
        stage('Generate Artifact') {
            steps {
                script {
                    if (isUnix()) {
                        sh './gradlew build -x test'
                    } else {
                        bat 'gradlew.bat build -x test'
                    }
                }
            }
            post {
                success {
                    archiveArtifacts artifacts: 'build/libs/*.jar', fingerprint: true
                }
            }
        }
    }
}
