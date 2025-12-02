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
        script {
          // Capture first line of java -version output
          String javaVersionOutput = sh(
            returnStdout: true,
            script: 'java -version 2>&1 | head -n 1'
          ).trim()

          // Example: openjdk version "17.0.17"
          // Extract the version inside the quotes
          String versionString = javaVersionOutput.find(/\"([0-9.]+)\"/)
            ?.replace('\"', '')

          // Extract major version (17 from 17.0.17)
          int major = versionString.split('\\.')[0].toInteger()

          if (major < 17) {
            error "❌ Java version ${versionString} is too old. Java 17 or higher is required."
          } else {
            echo "✔ Java version ${versionString} is OK."
          }
        }
      }
    }

    stage('Check Node Version') {
      steps {
        script {
          // Read Node.js version string, e.g., "v18.17.0"
          String nodeVersion = sh(returnStdout: true, script: 'node -v').trim()

          // Extract major version as integer
          int major = nodeVersion.replace('v', '').split('\\.')[0].toInteger()

          if (major < 20) {
            error "❌ Node.js version ${nodeVersion} is too old. Version 20 or higher is required."
          } else {
            echo "✔ Node.js version ${nodeVersion} is OK."
          }
        }
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
        sh 'rm -f package-lock.json'
        sh 'npm cache clean --force'
        sh 'node -v'
        sh 'npm -v'
        sh 'npm install --legacy-peer-deps'
        // generate autolinking
        sh 'npx react-native config'
       // sh 'npx react-native autolink'
      }
    }

    stage('Build Android APK') {
      steps {
        echo 'Cleaning Android build and caches...'
        sh 'cd android && chmod +x ./gradlew && ./gradlew clean cleanBuildCache'

        echo 'Building release APK with Java 17...'
        sh 'npx react-native generate-android
'
        // # sh 'cd android && chmod +x ./gradlew && ./gradlew assembleRelease -Dorg.gradle.java.home=/opt/jdk17'
        // sh '''
        //             cd android
        //             chmod +x ./gradlew
        //             ./gradlew :app:generateAutolinking --quiet || true
        //         '''
        //         // Verify the file was created
        //        // sh 'test -f android/build/generated/autolinking/autolinking.json && echo "autolinking.json generated!" || (echo "autolinking.json STILL missing!" && exit 1)'
        // sh './gradlew assembleRelease --stacktrace --info'
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