pipeline {
    agent any


    environment {
        JAVA_HOME = '/opt/java/openjdk'
        ANDROID_HOME = '/opt/android-sdk'
        PATH = "${JAVA_HOME}/bin:${ANDROID_HOME}/platform-tools:${ANDROID_HOME}/cmdline-tools/latest/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
        NODE_OPTIONS = '--max_old_space_size=4096'
    }

    stages {
        stage('Check Java Version') {
            steps {
                echo 'java version...'
                sh 'java -version'
                sh 'javac -version'
            }
        }

        stage('Check Gradle Version') {
            steps {
                echo 'Gradle version (using wrapper)...'
                sh 'cd android && chmod +x ./gradlew && ./gradlew --version'
            }
        }

        stage('Checkout SCM') {
            steps {
                echo 'Cloning the source repository...'
                git branch: 'main',
                    credentialsId: '36dc8b7d-7ec1-46c3-a1ac-243b32ffa1e6',
                    url: 'https://github.com/abdallahkadour/ReactNativeTest'
            }
        }

        stage('Install dependencies') {
            steps {
                echo 'Cleaning and installing Node modules...'
                sh 'rm -rf node_modules'
                sh 'node -v'
                sh 'npm -v'
                sh 'npm install'
            }
        }

        stage('Build Android APK') {
            steps {
                echo 'Cleaning Android build and caches...'
                sh 'cd android && chmod +x ./gradlew && ./gradlew clean cleanBuildCache'

                echo 'Building release APK with Java 17...'
               // # sh 'cd android && chmod +x ./gradlew && ./gradlew assembleRelease -Dorg.gradle.java.home=/opt/jdk17'
            
                sh 'cd android && chmod +x ./gradlew && ./gradlew assembleRelease --stacktrace --info'
            }
        }

        stage('Archive APK') {
            steps {
                echo 'Archiving release APK...'
                archiveArtifacts artifacts: 'android/app/build/outputs/apk/release/app-release.apk', fingerprint: true
                echo 'APK archived successfully!'
            }
        }
    }

    post {
        success {
            echo 'Android build and deployment completed successfully!'
        }
        failure {
            echo 'Error: Android build failed!'
        }
    }
}
