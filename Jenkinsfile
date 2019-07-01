timestamps {

	node () {

		stage ('test_gitlab - Checkout') {
			checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'otESg9458gd43Kx', url: 'https://github.com/rggarcia84/jpetstore-rob.git']]])
		}
		
		stage ('Build Artifact') {
			bat "mvn package " 
		}
		
		stage ('Call Docker Compose DES') {
			bat "docker-compose -f ../docker-compose.yml up -d db web"
		}
		
		stage ('UnDeploy Version DES') {
			bat "docker exec tomcatdes rm -rf /usr/local/tomcat/webapps/jpetstore.war" 
		}
		
		stage ('Deploy DES') {
			bat "docker cp -a target\\jpetstore.war tomcatdes:/usr/local/tomcat/webapps/"				
		}
		
		stage ('Reiniciando contenedor tomcatdes') {
			bat "docker restart tomcatdes" 
		}

		stage ('Call Docker Compose QAT') {	
			bat "docker-compose -f ../docker-compose.yml up -d db2 web2"
		}	
		
		stage ('UnDeploy Version QAT') {
			bat "docker exec tomcatqat rm -rf /usr/local/tomcat/webapps/jpetstore.war" 
		}
		
		stage ('Deploy QAT') {
			bat "docker cp -a target\\jpetstore.war tomcatqat:/usr/local/tomcat/webapps/" 				
		}
		
		stage ('Reiniciando contenedor tomcatqat') {
			bat "docker restart tomcatqat" 
		}				
	}
}


