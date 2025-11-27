pipeline {
    agent any
    tools {
        nodejs 'NodeJS'
        jdk 'Java17' // Name from Global Tool Configuration
    }

    environment {
        ANDROID_HOME = '/opt/android-sdk'
        // FIX: Explicit PATH setup to ensure all tools (npm, gradlew) are found by the Jenkins shell.
        PATH = "/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:${ANDROID_HOME}/platform-tools:${ANDROID_HOME}/cmdline-tools/latest/bin:${env.PATH}"
        NODE_OPTIONS = '--max_old_space_size=4096'
    }

    stages {
        stage('Check Java Version') {
            steps {
                sh 'java -version'
                sh 'javac -version'
            }
            stage('Check Gradle Version') {
                steps {
                    sh '''
            cd android
            ./gradlew --version
        '''
                }
            }
        }

        stage('Checkout SCM') {
            steps {
                echo 'Cloning the source repository...'

                git branch: 'main',
                    credentialsId: '36dc8b7d-7ec1-46c3-a1ac-243b32ffa1e6', // This ID was in your previous log
                    url: 'https://github.com/abdallahkadour/ReactNativeTest'
            }
        }
             // stage('Install dependencies') {
        //     steps {
        //         sh 'node -v'
        //         sh 'npm -v'
        //         sh 'npm install'
        //     }
        // }
        // stage('Build Android APK') {
        //     steps {
        //         echo ' Cleaning Android build...'
        //         sh 'cd android && chmod +x ./gradlew && ./gradlew clean'

        //         echo ' Building release APK...'
        //         // FIX 2: Used --java-launcher with the defined JAVA_HOME to override the project's invalid toolchain configuration.
        //         sh "cd android && chmod +x ./gradlew && ./gradlew assembleRelease --no-daemon --java-launcher \"${JAVA_HOME}/bin/java\""
        //     }
        // }

        stage('Build Android APK') {
            steps {
                echo ' Cleaning Android build...'
                sh 'cd android &&  chmod +x ./gradlew  &&  ./gradlew clean'

                echo ' Building release APK...'
                sh 'cd android && chmod +x ./gradlew && ./gradlew assembleRelease'
            }
        }

        stage('Archive APK') {
            steps {
                echo 'Archiving release APK...'
                archiveArtifacts artifacts: 'android/app/build/outputs/apk/release/app-release.apk', fingerprint: true
                echo ' APK archived successfully!'
            }
        }

    // stage('Upload to Nexus') {
    //     steps {
    //         echo "Uploading APK to Nexus Repository..."
    //         // Final step: Deploy the artifact to Nexus.
    //         sh 'curl -u admin:admin123 --upload-file android/app/build/outputs/apk/release/app-release.apk http://nexus.local/repository/apk-hosted/app-release.apk'
    //     }
    // }
    }

    post {
        success {
            echo ' Android build and deployment completed successfully!'
        }
        failure {
            echo 'Error: Android build failed!'
        }
    }
}
