import java.text.SimpleDateFormat

testEnvUrl = 'http://localhost:8089/'

node {
currentBuild.result = "SUCCESS"
  try{
   def mvnHome;
   def project_id;
   def artifact_id;
   def aws_s3_bucket_name;
   def aws_s3_bucket_region;
   def timeStamp;
   def userInput = false;
   def didTimeout = false;
   stage('Initalize'){
	   aws_s3_bucket_name = 'jvcdp-repo';
	   aws_s3_bucket_region = 'us-east-1';
	   timeStamp = getTimeStamp();
   }
   stage('Checkout') { // for display purposes
      checkout scm;
   }
   stage('Install Prerequisites'){
       run_playbook('installprerequisites.yaml')
   }
   stage('Configure AWS'){
       run_playbook('configureaws.yaml')
   }
    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'aws-deployuser', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']])  
    {
        stage('Download Artifacts'){
        userInput = false;
	didTimeout = false;
	
	try {
	    timeout(time: 15, unit: 'MINUTES') { // change to a convenient timeout for you
	        userInput = input(message: 'Should I redownload latest artifacts?', ok: 'Proceed', 
                        parameters: [booleanParam(defaultValue: true, 
                        description: 'If you would want to re-download latest artifacts, just tick the checkbox and click Proceed!',name: 'Yes?')])
	    }
	} catch(err) { // timeout reached or input false
	    def user = err.getCauses()[0].getUser()
	    if('SYSTEM' == user.toString()) { // SYSTEM means timeout.
	        didTimeout = true
	    } else {
	        userInput = false
	        echo "Aborted by: [${user}]"
	    }
	}
        if ((didTimeout)||(!userInput)) {
            // do something on timeout
            echo "no input was received before timeout"
            currentBuild.result = 'ABORTED'
        } else {
        run_playbook('downloadartifacts.yaml')
        }
        }
    }
   stage('Configure DB'){

    userInput = false;
	didTimeout = false;
	
	try {
	    timeout(time: 15, unit: 'MINUTES') { // change to a convenient timeout for you
	        userInput = input(message: 'Should configure the Postgresql DB?', ok: 'Proceed', 
                        parameters: [booleanParam(defaultValue: true, 
                        description: 'If you would want to configure the Postgresql DB, just tick the checkbox and click Proceed!',name: 'Yes?')])
	    }
	} catch(err) { // timeout reached or input false
	    def user = err.getCauses()[0].getUser()
	    if('SYSTEM' == user.toString()) { // SYSTEM means timeout.
	        didTimeout = true
	    } else {
	        userInput = false
	        echo "Aborted by: [${user}]"
	    }
	}
        if ((didTimeout)||(!userInput)) {
            // do something on timeout
            echo "no input was received before timeout"
            currentBuild.result = 'ABORTED'
        } else {
            run_playbook('setuppostgresqldb.yaml')
        } 
   }
   stage('Setup Dashboard UI'){
       userInput = false;
	didTimeout = false;
	
	try {
	    timeout(time: 2, unit: 'DAYS') { // change to a convenient timeout for you
	        userInput = input(message: 'Should I run the containers?', ok: 'Proceed', 
                        parameters: [booleanParam(defaultValue: true, 
                        description: 'If you would want to run the containers now, just tick the checkbox and click Proceed!',name: 'Yes?')])
	    }
	} catch(err) { // timeout reached or input false
	    def user = err.getCauses()[0].getUser()
	    if('SYSTEM' == user.toString()) { // SYSTEM means timeout.
	        didTimeout = true
	    } else {
	        userInput = false
	        echo "Aborted by: [${user}]"
	    }
	}
        if ((didTimeout)||(!userInput)) {
            // do something on timeout
            echo "no input was received before timeout"
            currentBuild.result = 'ABORTED'
        } else {
       run_playbook('setupdashboardui.yaml')
        }
   }
   stage('Setup Dashboard API'){
       userInput = false;
	didTimeout = false;
	
	try {
	    timeout(time: 2, unit: 'DAYS') { // change to a convenient timeout for you
	        userInput = input(message: 'Should I run the containers?', ok: 'Proceed', 
                        parameters: [booleanParam(defaultValue: true, 
                        description: 'If you would want to run the containers now, just tick the checkbox and click Proceed!',name: 'Yes?')])
	    }
	} catch(err) { // timeout reached or input false
	    def user = err.getCauses()[0].getUser()
	    if('SYSTEM' == user.toString()) { // SYSTEM means timeout.
	        didTimeout = true
	    } else {
	        userInput = false
	        echo "Aborted by: [${user}]"
	    }
	}
        if ((didTimeout)||(!userInput)) {
            // do something on timeout
            echo "no input was received before timeout"
            currentBuild.result = 'ABORTED'
        } else {
       run_playbook('setupdashboardapi.yaml')
        }
   }
   stage('Setup User API'){
       userInput = false;
	didTimeout = false;
	
	try {
	    timeout(time: 2, unit: 'DAYS') { // change to a convenient timeout for you
	        userInput = input(message: 'Should I run the containers?', ok: 'Proceed', 
                        parameters: [booleanParam(defaultValue: true, 
                        description: 'If you would want to run the containers now, just tick the checkbox and click Proceed!',name: 'Yes?')])
	    }
	} catch(err) { // timeout reached or input false
	    def user = err.getCauses()[0].getUser()
	    if('SYSTEM' == user.toString()) { // SYSTEM means timeout.
	        didTimeout = true
	    } else {
	        userInput = false
	        echo "Aborted by: [${user}]"
	    }
	}
        if ((didTimeout)||(!userInput)) {
            // do something on timeout
            echo "no input was received before timeout"
            currentBuild.result = 'ABORTED'
        } else {
       run_playbook('setupuserapi.yaml')
        }
   }
  }catch (err) {

        currentBuild.result = "FAILURE"

        throw err
    }
}

def getTimeStamp(){
	def dateFormat = new SimpleDateFormat("yyyyMMddHHmm")
	def date = new Date()
	return dateFormat.format(date);
}

def version() {
    def ver = readFile('pom.xml') =~ '<version>(.+)</version>'
    ver ? ver[0][1] : null
    def art = readFile('pom.xml') =~ '<artifactId>(.+)</artifactId>'
    art ? art[0][1] : null
    def pck = readFile('pom.xml') =~ '<packaging>(.+)</packaging>'
    pck ? pck[0][1] : null
	version = art+ver ? art + '-' + ver + '.' + pck : artifactId + '.jar'
	return version;
}

def mvn(args) {
    _mvnHome = tool 'Maven3.5.0'
    sh "${_mvnHome}/bin/mvn ${args}"
}

def runTests(duration) {
    node {
        checkout scm
        runWithServer {url ->
            mvn "-o -f sometests test -Durl=${url} -Dduration=${duration}"
        }
    }
}

def run_playbook(id) {
	sh "ansible-playbook ${id}";
}

def stop_container(container_name) {
    sh "docker stop ${container_name}"
}

def runWithServer(body) {
    def id = UUID.randomUUID().toString()
    deploy id
    try {
        body.call "${jettyUrl}${id}/"
    } finally {
        undeploy id
    }
}
