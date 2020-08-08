pipeline { 
    // this is an important setup or differentiator from native java jenkins pipe
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
    }

    stages {
        // CloneCode stage is commented as the repo is already cloned by the Jenkins pipe
        stage("CloneCode") {
            steps {
                script {
                    // Let's clone the source
                    echo 'Repo Checkout'
                    // git %GIT_URL%;
                }
            }
        }
        stage("Build") {
            steps {
                script {
                    sh 'pip install -r requirements.txt'
                    sh 'export FLASK_APP=flaskr'
                    sh 'export FLASK_ENV=development'
                    sh 'flask init-db'
                }
            }
        }
        stage("pytest") {
            steps {
                script {
                    sh "python3 -m pytest"
                }
            }
        }
        stage('Inspect_SonarQubeAnalytics') {
            steps {
                withSonarQubeEnv('SonarQube') { // this must match sonar server name from global configuraiton
                // if [[ "$CI_BRANCH_NAME" == 'Feature/*' ]] || [[ "$CI_BRANCH_NAME" == 'master' ]] || [[ "$CI_BRANCH_NAME" == 'release/*' ]]; then
                sh "${tool("SonarQube_Scanner")}/bin/sonar-scanner -Dsonar.projectKey=CI_Sample_Python \
                    -Dsonar.sources=. -Dsonar.host.url=http://sonarqube:9000 \
                    -Dsonar.login=ad62157c6bb0f42150da5975f25e3260660a9826"
                // fi
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
            echo 'JENKINS PIPELINE'
        }
        notBuilt {
            echo 'JENKINS PIPELINE NOT BUILT'
        }
        success {
            echo 'JENKINS PIPELINE SUCCESSFUL'
        }
        failure {
            echo 'JENKINS PIPELINE FAILED'
        }
        unstable {
            echo 'JENKINS PIPELINE WAS MARKED AS UNSTABLE'
        }
        changed {
            echo 'JENKINS PIPELINE STATUS HAS CHANGED SINCE LAST EXECUTION'
        }
        aborted {
            echo 'JENKINS PIPELINE ABORTED'
        }
    }
}