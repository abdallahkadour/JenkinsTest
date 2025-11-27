pipeline {
    agent any

    environment {
        ANDROID_HOME = '/opt/android-sdk'
        PATH = "$PATH:$ANDROID_HOME/platform-tools:$ANDROID_HOME/cmdline-tools/latest/bin:$PATH"
        NODE_OPTIONS = '--max_old_space_size=4096'
    }

    stages {
        stage('Clean & Install React Native Packages') {
            steps {
                sh 'echo $PATH'
                sh 'which /usr/bin/npm install'

                echo ' Cleaning old node_modules and lock files...'
                sh 'rm -rf node_modules/ package-lock.json yarn.lock'
                echo ' Installing npm packages...'
                sh '/usr/bin/npm install'
            }
        }

        stage('Build Android APK') {
            steps {
                echo ' Cleaning Android build...'
                sh 'cd android && ./gradlew clean'
                echo ' Building release APK...'
                sh 'cd android && ./gradlew assembleRelease'
            }
        }

        stage('Archive APK') {
            steps {
                echo 'Archiving release APK...'
                archiveArtifacts artifacts: 'android/app/build/outputs/apk/release/app-release.apk', fingerprint: true
                echo ' APK archived successfully!'
            }
        }
    }

    post {
        success {
            echo ' Android build completed successfully!'
        }
        failure {
            echo 'Error: Android build failed!'
        }
    }
}
