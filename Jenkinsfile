pipeline {
    // run on jenkins nodes tha has java 8 label
    agent { label '' }
    // global env variables
   // environment {
//        EMAIL_RECIPIENTS = 'mahmoud.romeh@gmail.com'
  //  }
    stages {

        stage('Build with unit testing') {
            steps {
                // Run the maven build
                script {
                    // Get the Maven tool.
                    // ** NOTE: This 'M3' Maven tool must be configured
                    // **       in the global configuration.
                    echo 'Pulling...' + env.BRANCH_NAME
                    def mvnHome = tool 'Maven 3.5.2'
                    if (isUnix()) {
                        def targetVersion = getDevVersion()
                        print 'target build version...'
                        print targetVersion
                        sh "'${mvnHome}/bin/mvn' -Dintegration-tests.skip=true -Dbuild.number=${targetVersion} clean package"
                        def pom = readMavenPom file: 'pom.xml'
                        // get the current development version
                        developmentArtifactVersion = "${pom.version}-${targetVersion}"
                        print pom.version
                        // execute the unit testing and collect the reports
                       // junit 'target/surefire-reports/TEST.xml'
                        archive 'target*//*.jar'
                    } else {
                        bat(/"${mvnHome}\bin\mvn" -Dintegration-tests.skip=true clean package/)
                        def pom = readMavenPom file: 'pom.xml'
                        print pom.version
                        junit '**//*target/surefire-reports/TEST-*.xml'
                        archive 'target*//*.jar'
                    }
                }

            }
        }




    }




}

def developmentArtifactVersion = ''
def releasedVersion = ''
// get change log to be send over the mail
@NonCPS
def getChangeString() {
    MAX_MSG_LEN = 100
    def changeString = ""

    echo "Gathering SCM changes"
    def changeLogSets = currentBuild.changeSets
    for (int i = 0; i < changeLogSets.size(); i++) {
        def entries = changeLogSets[i].items
        for (int j = 0; j < entries.length; j++) {
            def entry = entries[j]
            truncated_msg = entry.msg.take(MAX_MSG_LEN)
            changeString += " - ${truncated_msg} [${entry.author}]\n"
        }
    }

    if (!changeString) {
        changeString = " - No new changes"
    }
    return changeString
}

def sendEmail(status) {
    mail(
            to: "$EMAIL_RECIPIENTS",
            subject: "Build $BUILD_NUMBER - " + status + " (${currentBuild.fullDisplayName})",
            body: "Changes:\n " + getChangeString() + "\n\n Check console output at: $BUILD_URL/console" + "\n")
}

def getDevVersion() {
    def gitCommit = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
    def versionNumber;
    if (gitCommit == null) {
        versionNumber = env.BUILD_NUMBER;
    } else {
        versionNumber = gitCommit.take(8);
    }
    print 'build  versions...'
    print versionNumber
    return versionNumber
}

def getReleaseVersion() {
    def pom = readMavenPom file: 'pom.xml'
    def gitCommit = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
    def versionNumber;
    if (gitCommit == null) {
        versionNumber = env.BUILD_NUMBER;
    } else {
        versionNumber = gitCommit.take(8);
    }
    return pom.version.replace("-SNAPSHOT", ".${versionNumber}")
}

// if you want parallel execution , check below :
/* stage('Quality Gate(Integration Tests and Sonar Scan)') {
           // Run the maven build
           steps {
               parallel(
                       IntegrationTest: {
                           script {
                               def mvnHome = tool 'Maven 3.5.2'
                               if (isUnix()) {
                                   sh "'${mvnHome}/bin/mvn'  verify -Dunit-tests.skip=true"
                               } else {
                                   bat(/"${mvnHome}\bin\mvn" verify -Dunit-tests.skip=true/)
                               }
                           }
                       },
                       SonarCheck: {
                           script {
                               def mvnHome = tool 'Maven 3.5.2'
                               withSonarQubeEnv {
                                   // sh "'${mvnHome}/bin/mvn'  verify sonar:sonar -Dsonar.host.url=http://bicsjava.bc/sonar/ -Dmaven.test.failure.ignore=true"
                                   sh "'${mvnHome}/bin/mvn'  verify sonar:sonar -Dmaven.test.failure.ignore=true"
                               }
                           }
                       },
                       failFast: true)
           }
       }*/






