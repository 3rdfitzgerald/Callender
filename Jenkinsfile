@Library('qaas-ct-lib@rubyManual')_
import markel.qaas.pipeline.Cucumber
import groovy.json.JsonOutput

pipeline {
	agent { node { label 'master' } }
	
	environment {
		PIPELINE_TYPE = 'manual'
	}
	
	parameters {
		string(name: 'project', 	defaultValue: '', description: 'The name of the project being tested')
        string(name: 'environment', defaultValue: '', description: 'The environment that tests are being run against')
		string(name: 'repository', 	defaultValue: '', description: 'The repository of the project')
		string(name: 'branch', 		defaultValue: '', description: 'The repository branch being tested')
		string(name: 'directory', 	defaultValue: '', description: 'The directory of the folder containing tests')
	}

	stages {
		stage ('Prepare Environments') {
			steps {
				prepareEnvironments(params.project, params.environment, params.repository, params.branch, params.directory)
			}
		}
		stage ('Execute Tests') {
			steps {
				executeManualTests()
			}
		}
		stage ('Report Results') {
			steps {
				script {
					#!/usr/bin/env groovy
					import markel.qaas.pipeline.BuildData
					import markel.qaas.pipeline.JenkinsInformation

					def reportDirectory = "${JENKINS_HOME}\\"
					
					for(path in JOB_NAME.split('/')) {
						reportDirectory += "jobs\\${path}\\"
					}
					
					reportDirectory += "builds\\${BUILD_NUMBER}\\archive\\reports"
		
					step([
						$class: 'CucumberReportPublisher', 
						jsonReportDirectory: reportDirectory, 
						fileIncludePattern: '*.json'
					])
					if (BuildData.getPipelineType() == "performance") {
						perfReport compareBuildPrevious: true, modeOfThreshold: true, modePerformancePerTestCase: true, modeThroughput: true, sourceDataFiles: reportDirectory + '/*.jtl'
					}
					
					// Move cucumber reports to central directory for filebeat pickup
					if (isUnix()) {
						// To Do
					} else {
						bat("copy /Y \"${reportDirectory}\" \"${JenkinsInformation.CENTRAL_REPORT_DIRECTORY_WINDOWS}\"")
					}

					def emails = BuildData.getEmailList()
					def project = BuildData.getProject()
					def environment = BuildData.getEnvironment()
					
					new File('latest_build_details.txt').write(project + ',' + environment + ',' + env.BUILD_URL + ',' + {(currentBuild.getResult() == null) ? "SUCCESS" : currentBuild.getResult()}() )
						
					def emailList = ""
					for(int i = 0; i < emails.size(); i++) {
						emailList += emails[i] + ','
					}
					
					if (BuildData.getPipelineType() == "performance") {
						emailext(
							to: emailList,
							subject: "[Jenkins] FINISHED: ${project} Pipeline",
							body: '${SCRIPT, template="' + JenkinsInformation.PERFORMANCE_EMAIL_FILE + '"}'
						)
					} else {
						emailext(
							to: emailList,
							subject: "[Jenkins] FINISHED: ${project} Pipeline",
							body: '${SCRIPT, template="' + JenkinsInformation.CUCUMBER_EMAIL_FILE + '"}'
						)
					}
				}

				/*script {
					def json = readJSON text: Cucumber.getResults()
                    writeJSON file: 'manual.json', json: json
                }
                archiveArtifacts 'manual.json'
				step([
                    $class: 'CucumberReportPublisher',
                    jsonReportDirectory: "${JENKINS_HOME}/jobs/${JOB_NAME}/builds/${BUILD_NUMBER}/archive",
                    fileIncludePattern: '*.json'
                ])*/
				//generateReports()
				//sendEmails()
			}
		}
	}
	/*post {
		always {
			logstashSend failBuild: true, maxLines: 1000
		}
	}*/
}
