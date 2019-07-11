timestamps {

	node () {

		stage ('test_gitlab - Checkout') {
			checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'otESg9458gd43Kx', url: 'https://github.com/rggarcia84/jpetstore-rob.git']]])
		}
		
		stage ('Build Artifact') {
			bat "mvn package " 
		}
		
		stage ('Call Docker Compose DES') {
			bat "docker-compose -f ../docker-compose-clean2.yml up -d db web"
		}
		
		stage ('Call Docker Compose QAT') {	
			bat "docker-compose -f ../docker-compose-clean2.yml up -d db2 web2"
		}	
		
		stage ('UnDeploy Version DES') {
			bat "docker exec tomcatdes rm -rf /opt/apache-tomcat-8.5.37/webapps/jpetstore.war" 
		}
		
		stage ('UnDeploy Version QAT') {
			bat "docker exec tomcatqat rm -rf /opt/apache-tomcat-8.5.37/webapps/jpetstore.war" 
		}
		
		stage ('Deploy DES') {
			bat "docker cp -a target\\jpetstore.war tomcatdes:/opt/apache-tomcat-8.5.37/webapps/"				
		}
		
		stage ('Deploy QAT') {
			bat "docker cp -a target\\jpetstore.war tomcatqat:/opt/apache-tomcat-8.5.37/webapps/" 				
		}
		
		stage ('Reiniciando contenedor tomcatdes') {
			bat "docker restart tomcatdes" 
		}
		
		stage ('Reiniciando contenedor tomcatqat') {
			bat "docker restart tomcatqat" 
		}	
		
		stage ('Push tomcat to Registry') {
			bat "docker commit tomcatqat imagetomcatqat:latest"
		    bat "docker login -u robggarcia84 -p kF45gB68w"
		    bat "docker tag imagetomcatqat:latest robggarcia84/tomcatdes2:v.${env.BUILD_NUMBER}"
		    bat "docker push robggarcia84/tomcatdes2:v.${env.BUILD_NUMBER}"				
		}	
		
		stage ('Deployment K8s') {
			withKubeConfig([credentialsId: 'token-k8s',
					serverUrl: 'https://localhost:6445',
					contextName: 'docker-for-desktop',
					clusterName: 'docker-for-desktop-cluster'
				]) {
						bat "kubectl create namespace jpetstore-prd"
						bat "kubectl apply -f deploy-mysql.yaml"
						bat "kubectl apply -f deploy-tomcat_ETCHOSTS.yaml"
						bat "kubectl apply -f svc-mysql.yaml"
						bat "kubectl apply -f svc-tomcat.yaml"
				}	
		}	
	}   
}


