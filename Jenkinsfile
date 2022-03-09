pipeline {
    agent any

	parameters {
		choice(
			name: 'BRANCH', 
			choices: [
			 'master', 
			 'develop', 
			 'Borko',
			 'Ala Bala'
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
			defaultValue: 'ArgentaRunTLC', 
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
                echo 'checkout'
		script{
                    checkout([$class: 'GitSCM',
                              branches: [[name: '*/main']],
                              extensions: [],
                              userRemoteConfigs: [[url: 'https://github.com/balushev/python_test.git']]])
                }
            }
        }
        stage('Run Python Requirements'){
            steps {
                echo 'bat pip install -r requirements.txt'
            }
        }
        stage('Run Tests') {
            steps {
                echo 'Execute Tests'
                echo "Running ${env.BUILD_ID} on ${env.JENKINS_URL}"
                echo "Jenkins Workspace ${env.WORKSPACE}"
                echo "JOB NAME ${env.JOB_NAME}"
                script {
                    
					if (params.OVERWRITE_COMMAND == '') {
						if (params.ENABLE_ALLURE_REPORTS == true) {
						    echo 'bat "behave --tags ${params.TAG} -f allure_behave.formatter:AllureFormatter -o allure-reports ./features -D browser=${params.BROWSER} -D env=${params.ENVIRONMENT} -D tenant=${params.TENANT}"'
						} else {
							echo 'bat "behave --tags ${params.TAG} ./features -D browser=${params.BROWSER} -D env=${params.ENVIRONMENT} -D tenant=${params.TENANT}"'
						}
					} 
					else {
						echo 'bat "${params.OVERWRITE_COMMAND}"'
					}
                }
            }
        }
    }
    post('Publish report') {
        always {
            
			echo 'Generate Allure report'
			
// 			script {
//                 allure([
//                         includeProperties: false,
//                         jdk: '',
//                         properties: [],
//                         reportBuildPolicy: 'ALWAYS',
//                         results: [[path: 'allure-reports']]
//                 ])
            // }
        }
		
        failure {
            echo 'Failure'
        }
        
		cleanup {
			echo 'cleanWs'
        }
    }
}
