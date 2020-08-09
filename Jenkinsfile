pipeline { 
    // this is an important setup or differentiator from native java jenkins pipe
    // requires a node context while ‘agent none’ was specified
    agent any
    
    // This displays colors using the 'xterm' ansi color map in the console output
    options {
        ansiColor('xterm')
        skipStagesAfterUnstable()
        }

    environment {
        SONARQUBE_URL = 'http://sonarqube:9000'
        NEXUS_INSTANCE = 'sonatypeNexus'
        NEXUS_REPOSITORY = "CI_Sample_Python"
        // // Repository where we will upload the artifact
        NEXUS_REPOSITORY_RELEASES = "python-releases"
        NEXUS_REPOSITORY_SNAPSHOTS = "python-snapshots"
        YOUR_REPO = "${currentBuild.fullProjectName}"
    }

    stages {
        // CloneCode stage is commented as the repo is already cloned by the Jenkins pipe
        stage("CloneCode") {
            steps {
                script {
                    sh 'printenv'
                    // Let's clone the source
                    echo 'Repo Checkout'
                    // git %GIT_URL%;
                    stash(name: 'scm', includes: '*') 
                }
            }
        }
        stage('Build') {
            agent {
                docker {image 'python:3.8-alpine' 
                        args '-v ${PWD}:/usr/src/app -w /usr/src/app'
                        reuseNode true  } }
            steps {
                sh 'python -m py_compile flaskr/*.py tests/*.py'
                stash(name: 'compiled-results', includes: 'flaskr/*.py*') 
                sh 'python setup.py develop'
            }
        }

        stage("setup_flaskr") {
            steps {
                script {
                    sh 'pip install -e .'
                    // sh 'pip install -r requirements.txt'
                    sh 'export FLASK_APP=flaskr'
                    sh 'export FLASK_ENV=development'
                    sh 'flask init-db'
                    sh 'python setup.py develop'
                }
            }
        }
        stage('Test') {
            agent {docker {image 'qnib/pytest' 
                            args '-v ${PWD}:/usr/src/app -w /usr/src/app'
                            reuseNode true}}
            steps {
                sh 'pip install -e .'
                sh 'py.test --verbose --junit-xml test-reports/results.xml tests/*.py'
            }
            post {always {junit 'test-reports/results.xml'}}
        }
        stage('Inspect_SonarQubeAnalytics') {
            steps {
                script {
                    def scannerHome = tool 'sonarQube';
                    withSonarQubeEnv("sonarQube") {
                        // sh "${tool("sonarQube")}/bin/sonar-scanner -Dsonar.projectKey=CI_Sample_Python \
                        // -Dsonar.sources=. -Dsonar.host.url=http://sonarqube:9000 \
                        // -Dsonar.login=09949926d3d8c85fd2b9c0cf64cacf43ff683a43"
                        sh "docker run --rm -e SONAR_HOST_URL=${SONARQUBE_URL} \
                        -e SONAR_Login=09949926d3d8c85fd2b9c0cf64cacf43ff683a43 \
                        -v ${YOUR_REPO}:/usr/src sonarsource/sonar-scanner-cli"
                    }
                }
            }
        }
        stage('Deliver') {
            agent any
            environment {
                VOLUME = '$(pwd)/flaskr:/src, $(pwd)/tests:/tests'
                IMAGE = 'prabha6kar/CI_Sample_Python:flaskr_blog'
            }
            steps {
                dir(path: env.BUILD_ID) {
                    unstash(name: 'compiled-results')
                    sh "docker run --rm -v ${VOLUME} ${IMAGE} 'flaskr run'"
                }
            }
            post {
                success {
                    archiveArtifacts "${env.BUILD_ID}/sources/dist/add2vals"
                    sh "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'"
                }
            }
        }
    //     stage("PackagePublishToNexus") {
    //         steps {
    //             script {
    //                 // Read setup.cfg file using 'readProperties' step , this step 'readProperties' is included in: https://plugins.jenkins.io/pipeline-utility-steps
    //                 props = readProperties file: "setup.cfg";
    //                 // Find built artifact under target folder
    //                 filesByGlob = findFiles(glob: "target/*.${props.metadata}");
    //                 // Print some info from the artifact found
    //                 echo "${filesByGlob[0].name} ${filesByGlob[0].version} ${filesByGlob[0].url} ${filesByGlob[0].license} ${filesByGlob[0].maintanier}"
    //                 // Extract the path from the File found
    //                 artifactPath = filesByGlob[0].path;
    //                 // Assign to a boolean response verifying If the artifact name exists
    //                 artifactExists = fileExists artifactPath;
    //                 if(artifactExists) {
    //                     echo "*** File: ${artifactPath}, packaging: ${props.name}, version ${props.version}";

    //                     nexusPublisher nexusInstanceId: NEXUS_INSTANCE, 
    //                         nexusRepositoryId: NEXUS_REPOSITORY, 
    //                         packages: [
    //                             [$class: 'PyPiPackage', 
    //                             mavenAssetList: [
    //                                 [classifier: '', extension: '', 
    //                                 filePath: artifactPath]
    //                                 ], 
    //                             mavenCoordinate: [packaging: props.name, version: props.version]
    //                                 ]
    //                             ]
    //                 } else {
    //                     error "*** File: ${artifactPath}, could not be found";
    //                 }
    //             }
    //         }
    //     }
    }
    
    post {
        always {
            echo 'JENKINS PIPELINE - ${env.BUILD_URL}'
        }
        notBuilt {
            echo '${env.BUILD_URL} JENKINS PIPELINE NOT BUILT'
        }
        success {
            echo '${env.BUILD_URL} JENKINS PIPELINE SUCCESSFUL'
        }
        failure {
            echo '${env.BUILD_URL} JENKINS PIPELINE FAILED'
        }
        unstable {
            echo '${env.BUILD_URL} JENKINS PIPELINE WAS MARKED AS UNSTABLE'
        }
        changed {
            echo '${env.BUILD_URL} JENKINS PIPELINE STATUS HAS CHANGED SINCE LAST EXECUTION'
        }
        aborted {
            echo '${env.BUILD_URL} JENKINS PIPELINE ABORTED'
        }
    }
}