node ('Ubuntu-App-Agent') {  
    def app
    stage('Cloning Git') {
       // Let's make sure we have the repository cloned to our workspace
        checkout scm
    }  
    
    stage('SCA'){
        build 'Security-SCA-Snyk'
    }
    
    stage("docker_scan"){
      sh '''
        docker run -d --name db arminc/clair-db
        sleep 15 # wait for db to come up
        docker run -p 6060:6060 --link db:postgres -d --name clair arminc/clair-local-scan
        sleep 1
        DOCKER_GATEWAY=$(docker network inspect bridge --format "{{range .IPAM.Config}}{{.Gateway}}{{end}}")
        wget -qO clair-scanner https://github.com/arminc/clair-scanner/releases/download/v8/clair-scanner_linux_amd64 && chmod +x clair-scanner
        ./clair-scanner --ip="$DOCKER_GATEWAY" myapp:latest || exit 0
      '''
    }
    
    stage('SAST'){
        build 'Security-SAST-SonarQube'
    }

    stage('Build-and-Tag') {
    // This builds the actual image; synonymous to docker build on the command line
        app = docker.build("ptomar25/snakegame")
    }
    
    stage('Post-to-DockerHub') {
        docker.withRegistry('https://registry.hub.docker.com', 'DockerHub') {
            app.push("Jenkins")
        	}
    }
    
    stage('Image-Scanner-1') {
        build 'Security-Image-Aqua'
    }
    
    stage('Image-Scanner-2') {
        build 'Security-Image-Anchore'
    }
    
    stage('Pull-Image-Server') {
         sh "docker-compose down"
         sh "docker-compose up -d"
    }
    
    stage('DAST-1') {
        build 'Security-DAST-OWASP_ZAP'
    }
    
    stage('DAST-2') {
        build 'Security-DAST-Arachni'
    }
}
