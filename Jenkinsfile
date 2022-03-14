pipeline {
    
	agent{ 
		node {
		    label 'selenium’ 
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
			defaultValue: 'ArgentaRunLCT_1', 
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
		string(
			name: 'OVERWRITE_COMMAND', 
			defaultValue: '', 
			description: 'This command will overwrite all set parameters'
		)
	}
	
	stages {
		stage('Checkout SCM') {
		    steps {
			echo ' ============================================> Execute Checkout SCM <===================================='
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
				echo ' =======================================> Execute Run Python Requirements <=============================='
				bat 'pip install -r requirements.txt'
		    }
		}
		stage('Tests') {
		    steps {
				echo ' ===============================================> Execute Tests <========================================'
				echo "info: Running ${env.BUILD_ID} on ${env.JENKINS_URL}"
				echo "info: Jenkins Workspace ${env.WORKSPACE}"
				echo "info: JOB NAME ${env.JOB_NAME}"
				script {

					if (params.OVERWRITE_COMMAND == '') {
						if (params.ENABLE_ALLURE_REPORTS == true) {
							bat "behave --tags ${params.TAG} -f allure_behave.formatter:AllureFormatter -o allure-reports ./features -D browser=${params.BROWSER} -D env=${params.ENVIRONMENT} -D tenant=${params.TENANT}"
						} 
						else {
							bat "behave --tags ${params.TAG} ./features -D browser=${params.BROWSER} -D env=${params.ENVIRONMENT} -D tenant=${params.TENANT}"
						}
					} 
					else {
						bat "${params.OVERWRITE_COMMAND}"
					}
					assert 1 == 2
				}
		    }
		}
	}
	post('Publish report') {
		always {
			script {
				if (params.ENABLE_ALLURE_REPORTS == true){
					allure([
						includeProperties: false,
						jdk: '',
						properties: [],
						reportBuildPolicy: 'ALWAYS',
						results: [[path: 'allure-reports']],
						report: 'C:/Allure Reports Repo'
					])
				}
			}
		}

		failure {
			script {
				if (params.ENABLE_SEND_EMAILS == true) {
					emailext(
					to: "borislav.balushev@dxc.com",
					subject: "[ Jenkins ][ ${ENVIRONMENT} ] => Automation Tests Execution Summary",
					body: 'Some message !!!',  
					attachLog: true, 
					attachmentsPattern: '*.log'
					)
				}
			}
		}

		cleanup {
			cleanWs(
				cleanWhenNotBuilt: false,
				deleteDirs: true,
				disableDeferredWipeout: true,
				notFailBuild: true,
				patterns: [[pattern: '**/*', type: 'INCLUDE'], [pattern: '.propsfile', type: 'EXCLUDE']]
				)
		}
	}
}
