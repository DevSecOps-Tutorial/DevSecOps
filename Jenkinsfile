node ('Ubuntu-App-Agent') {  
    def app
    stage('Cloning Git') {
       // Let's make sure we have the repository cloned to our workspace
        checkout scm
    }  
    
    stage('SAST-1'){
        build 'Security-SAST-Snyk'
    }
    
    stage('SAST-2'){
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
