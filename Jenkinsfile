pipeline {
    // run on jenkins nodes tha has java 8 label
    agent { label '' }
    // global env variables
    environment {
        EMAIL_RECIPIENTS = 'omar.sarih@gmail.com'
    }
    stages {

        stage('Build with unit testing') {
            steps {
                // Run the maven build
                script {
                     print 'aaaaaaaaaaaa'
                    // Get the Maven tool.
                    // ** NOTE: This 'M3' Maven tool must be configured
                    // **       in the global configuration.
                    echo 'Pulling...' + env.BRANCH_NAME
                    def mvnHome = tool 'Maven 3.5.2'
                    print mvnHome
                    if (isUnix()) {
                                             print 'bbbbbbbbbb'

                        def targetVersion = getDevVersion()
                        print 'target build version...'
                        print targetVersion
                                                                     print 'ccccccccc'

                        sh "'${mvnHome}/bin/mvn' -Dintegration-tests.skip=true -Dbuild.number=${targetVersion} clean package"
                        def pom = readMavenPom file: 'pom.xml'
                        // get the current development version
                        developmentArtifactVersion = "${pom.version}-${targetVersion}"
                                                                     print 'ddddddddd'

                        print pom.version
                        // execute the unit testing and collect the reports
                        junit '**//*target/surefire-reports/TEST-*.xml'
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
    





