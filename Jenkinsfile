#!groovy​
BUILD_URL = "${env.BUILD_URL}"
BUILD_TYPE = "${env.BUILD_TYPE}" // e.g. "Debug", "Release"
BUILD_NUMBER = "${env.BUILD_NUMBER}"
EMAIL = "${env.EMAIL}"
PORT_EMU_1 = "${env.PORT_EMU_1}"
PORT_EMU_2 = "${env.PORT_EMU_2}"

REPORT_PATH_UNIT_TESTS = 'app/build/test-results/testDebugUnitTest/debug'
REPORT_PATH_INSTRUMENTED_TESTS = 'app/build/outputs/androidTest-results/connected'

node {
    checkout()
    assemble()
    runUnitTests()
    runInstrumentedTests()
    sendEmailSuccess()
}

void checkout() {
    stage('Checkout') {
        checkout scm
    }
}

void assemble() {
    stage('Assemble') {
        // Remove reports from previous build
        sh "rm -rf ../htmlreports/"
        sh "./gradlew clean assemble$BUILD_TYPE"
    }
}

void runUnitTests() {
    stage('Unit Tests') {
        try {
            sh "./gradlew --stacktrace testDebug"
        } catch (error) {
            sendEmailFail()
            throw error
        }

        Throwable error = publishUnitTestReport()
        if (error != null) {
            sendEmailFail()
            throw error
        }
    }
}

void runInstrumentedTests() {
    stage('Instrumented Tests') {
        sh './gradlew assembleDebugAndroidTest'
        parallel(
                emu1: {
                    runInstrumentedTestOnEmu("-Pandroid.testInstrumentationRunnerArguments.class=com.google.samples.apps.topeka.activity.SignInActivityTest -Pdevices=emulator-$PORT_EMU_1")
                },
                emu2: {
                    runInstrumentedTestOnEmu("-Pandroid.testInstrumentationRunnerArguments.class=com.google.samples.apps.topeka.activity.CategorySelectionActivityTest -Pdevices=emulator-$PORT_EMU_2")
                },
                failFast: true
        )
        Throwable error = publishInstrumentedTestReport()
        if (error != null) {
            sendEmailFail()
            throw error
        }
    }
}

void runInstrumentedTestOnEmu(String param) {
    try {
        sh "./gradlew connectedDebugAndroidTest --stacktrace --info $param"
    } catch (error) {
        sendEmailFail()
        throw error
    }
}

void publishUnitTestReport() {
    Throwable error = null

    try {
        publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: REPORT_PATH_UNIT_TESTS, reportFiles: 'index.html', reportName: 'Unit tests report'])
    } catch (e) {
        error = e
    }

    error
}

void publishInstrumentedTestReport() {
    Throwable error = null

    try {
        publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: REPORT_PATH_INSTRUMENTED_TESTS, reportFiles: 'index.html', reportName: 'Instrumented tests report'])
    } catch (e) {
        error = e
    }

    error
}

void sendEmailSuccess() {
    emailext body: BUILD_URL, subject: "Build $BUILD_NUMBER succeeded", to: EMAIL
}

void sendEmailFail() {
    if(currentBuild.result != 'FAILURE') {
        currentBuild.result = 'FAILURE'
        step([$class: 'Mailer', notifyEveryUnstableBuild: true, recipients: EMAIL, sendToIndividuals: true])
    }
}