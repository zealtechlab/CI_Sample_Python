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
        NEXUS_INSTANCE = 'sonatypeNexus'
        NEXUS_REPOSITORY = "CI_Sample_Python"
        // // Repository where we will upload the artifact
        NEXUS_REPOSITORY_RELEASES = "python-releases"
        NEXUS_REPOSITORY_SNAPSHOTS = "python-snapshots"
        // SONARQUBE_URL = 'http://sonarqube:9000'
        SONARQUBE_URL = 'http://172.18.0.3:9000'
        SONAR_CACHE_DIR = "${PWD}/sonar_cache"
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
        stage('Compile_Build') {
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
        stage('setup_flaskr_Pytest') {
            steps {
                sh 'pip install -e .'
                sh 'FLASK_APP=flaskr flask init-db'
                sh 'pytest -v --junitxml="test-reports/results.xml"'
            }
            post {always {junit 'test-reports/results.xml'}}
        }
        stage('Inspect_SonarQubeAnalytics') {
            steps {
                script {
                    def scannerHome = tool 'sonarQube_Scanner';
                    withSonarQubeEnv("sonarQube") {
                        sh "${tool("sonarQube_Scanner")}/bin/sonar-scanner"
                        // -Dsonar.host.url=http://sonarqube:9000 \
                        // -Dsonar.login=454d59b6e686ff8a6cf6f984ed2553e615c33cfd"
                        
                        // ERROR: You must define the following mandatory properties for 'Unknown': sonar.projectKey
                        // sh "docker run --rm -e SONAR_HOST_URL=${SONARQUBE_URL} \
                        // -e SONAR_Login=09949926d3d8c85fd2b9c0cf64cacf43ff683a43 \
                        // -v ${PWD}:/usr/src \
                        // -v ${SONAR_CACHE_DIR}:/opt/sonar-scanner/.sonar/cache \
                        // sonarsource/sonar-scanner-cli"
                    }
                }
            }
        }
        // stage('Deliver') {
        //     agent any
        //     environment {
        //         VOLUME = '$(pwd)/flaskr:/src, $(pwd)/tests:/tests'
        //         IMAGE = 'prabha6kar/ci_sample_python:flaskr_blog'
        //     }
        //     steps {
        //         dir(path: BUILD_ID) {
        //             unstash(name: 'compiled-results')
        //             sh "pwd && ls -al"
        //             sh "cat mypass.txt | docker login --username prabha6kar --password-stdin"
        //             sh "docker run --rm -v '${VOLUME}' '${IMAGE}' 'FLASK_APP=flaskr flaskr run'"
        //             sh "docker logout"
        //         }
        //     }
        //     post {
        //         success {
        //             archiveArtifacts "${env.BUILD_NUMBER}/sources/dist/flaskr_blog"
        //             sh "docker run --rm -v '${VOLUME}' '${IMAGE}' 'rm -rf build dist'"
        //         }
        //     }
        // }
        stage("PackagePublishToNexus") {
            steps {
                script {
                    echo "*** File: ${BRANCH_NAME}";

                    nexusPublisher nexusInstanceId: NEXUS_INSTANCE, 
                        nexusRepositoryId: NEXUS_REPOSITORY, 
                        packages: [
                            [$class: 'PyPiPackage', 
                            mavenAssetList: [
                                [classifier: '', extension: '', 
                                filePath: $JOB_NAME]
                                ], 
                            mavenCoordinate: [packaging: $BRANCH_NAME, version: $BUILD_ID]
                                ]
                            ]
                }
            }
        }
    }
    
    post {
        always {
            echo 'JENKINS PIPELINE - ${env.BUILD_URL}, ${env.BUILD_NUMBER}'
        }
        notBuilt {
            echo '${env.BUILD_NUMBER} ${env.BUILD_URL} JENKINS PIPELINE NOT BUILT, STOPPED at STAGE - ${env.STAGE_NAME}'
        }
        success {
            echo '${env.BUILD_NUMBER} ${env.BUILD_URL} JENKINS PIPELINE SUCCESSFUL'
        }
        failure {
            echo '${env.BUILD_NUMBER} ${env.BUILD_URL} JENKINS PIPELINE FAILED at Stage - ${env.STAGE_NAME}'
        }
        unstable {
            echo '${env.BUILD_NUMBER} ${env.BUILD_URL} JENKINS PIPELINE WAS MARKED AS UNSTABLE'
        }
        changed {
            echo '${env.BUILD_NUMBER} ${env.BUILD_URL} JENKINS PIPELINE STATUS HAS CHANGED SINCE LAST EXECUTION'
        }
        aborted {
            echo '${env.BUILD_NUMBER} ${env.BUILD_URL} JENKINS PIPELINE ABORTED'
        }
    }
}