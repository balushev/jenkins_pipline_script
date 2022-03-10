pipeline {
    
	agent{ 
		node {
		    label 'AUTOMATION'
		}
	}
	
	parameters {
		choice(
			name: 'BRANCH', 
			choices: [
			 'main',
			 'master', 
			 'develop', 
			 'Borko'
			], 
			description: 'Please select the Branch'
		)
		string(
			name: 'TAG', 
			defaultValue: '@sanity', 
			description: 'Please fill testing tag')
		choice(
			name: 'BROWSER', 
			choices: [
				'Chrome', 
				'Firefox', 
				'Edge', 
				'Headless'
			], 
			description: 'Please choose your browser'
		)
		string(
			name: 'ENVIRONMENT', 
			defaultValue: 'ArgentaRunLCT', 
			description: 'Please fill your environment'
		)
		string(
			name: 'TENANT', 
			defaultValue: 'default', 
			description: 'Please fill your tenant'
		)
		booleanParam(
			name: 'ENABLE_ALLURE_REPORTS', 
			defaultValue: false, 
			description: 'Please make your choose'
		)
		booleanParam(
			name: 'ENABLE_SEND_EMAILS', 
			defaultValue: false, 
			description: 'Please make your choose'
		)
		string(
			name: 'OVERWRITE_COMMAND', 
			defaultValue: '', 
			description: 'This command will overwrite all set parameters'
		)
	}
	
	stages {
		stage('Checkout SCM') {
		    steps {
			echo ' => Execute Checkout SCM'
			script{
			    checkout([$class: 'GitSCM',
				      branches: [[name: params.BRANCH]],
				      extensions: [],
				      userRemoteConfigs: [[url: 'https://github.com/balushev/python_test.git']]])
			}
		    }
		}
		stage('Python Requirements'){
		    steps {
			echo ' => Execute Run Python Requirements'
			echo 'bat pip install -r requirements.txt'
		    }
		}
		stage('Tests') {
		    steps {
			echo ' => Execute Tests'
			echo "info: Running ${env.BUILD_ID} on ${env.JENKINS_URL}"
			echo "info: Jenkins Workspace ${env.WORKSPACE}"
			echo "info: JOB NAME ${env.JOB_NAME}"
			script {

				if (params.OVERWRITE_COMMAND == '') {
					if (params.ENABLE_ALLURE_REPORTS == true) {
					    echo 'bat "behave --tags ${params.TAG} -f allure_behave.formatter:AllureFormatter -o allure-reports ./features -D browser=${params.BROWSER} -D env=${params.ENVIRONMENT} -D tenant=${params.TENANT}"'
					} 
					else {
						echo 'bat "behave --tags ${params.TAG} ./features -D browser=${params.BROWSER} -D env=${params.ENVIRONMENT} -D tenant=${params.TENANT}"'
					}
				} 
				else {
					echo 'bat "${params.OVERWRITE_COMMAND}"'
				}
				assert 1 == 2
			}
		    }
		}
	    }
	    post('Publish report') {
		always {
			echo 'always'
// 			script {
// 				allure([
// 					includeProperties: false,
// 					jdk: '',
// 					properties: [],
// 					reportBuildPolicy: 'ALWAYS',
// 					results: [[path: 'allure-reports']]
// 				])
// 		    	}
		}

		failure {
		    script {
			if (params.ENABLE_SEND_EMAILS == true) {
			    emailext body: 'It is alive',
				  subject: "Automation Tests Execution Summary",
				  to: "b_dimov@abv.bg"
			}
		    }
		}

		cleanup {
			echo 'clean_WS'
// 			cleanWs(cleanWhenNotBuilt: false,
// 				deleteDirs: true,
// 				disableDeferredWipeout: true,
// 				notFailBuild: true,
// 				patterns: [[pattern: '**/*', type: 'INCLUDE'], [pattern: '.propsfile', type: 'EXCLUDE']])
		}
    	}
}
