stage('Build') {
	steps {
		sh 'docker build . -f /var/jenkins_home/Dockerbuild -t builder'
		echo 'building finished'
	}
}
stage('Test'){
	steps{
		sh 'docker build . -f /var/jenkins_home/Dockertest -t tester'
		sh 'docker run tester'
		echo 'finished tests'
	}
}

stage('Deploy'){
	steps{
		echo 'Deploy'
		sh 'docker build . -f /var/jenkins_home/Dockerdeploy -t deployer'
		sh 'docker stop $(docker ps -aq)'
		sh 'docker run -d -p 8081:8080 deployer'
		echo 'finished deployment'
	}

}

stage('Publish'){
	when {
		expression {
			return params.PROMOTE == true
		}
	}
	agent{			
		docker {
			image 'builder:latest' 
			args '-u root' 
			}
	}

	steps{
		echo 'publish'
		sh 'npm install -g npm@latest'

		sh 'git config user.email "qubasiekno@gmail.com"'
		sh 'git config user.name "JSDenton"'

		sh "npm version ${params.VERSION}.${BUILD_NUMBER}"
		load '/var/jenkins_home/token'
		withEnv(["TOKEN=${NPM_TOKEN}"]) {

			sh 'echo "//registry.npmjs.org/:_authToken=${TOKEN}" >> ~/.npmrc'
			sh 'npm publish --access public' 

		}
		echo 'finished publishing' 
	}
}
