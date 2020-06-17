node ('master') {  
    def app
    stage('Cloning Git') {
       // Let's make sure we have the repository cloned to our workspace
        checkout scm
    }  
    /* stage('SAST'){
        build 'SECURITY-SAST-SNYK'
    } */

    stage('Build-and-Tag') {
    // This builds the actual image; synonymous to docker build on the command line
        app = docker.build("ptomar25/snakegame")
    }
    
    stage('Post-to-DockerHub') {
        docker.withRegistry('https://registry.hub.docker.com', 'DockerHub') {
            app.push("Jenkins")
        			}
    }
    
    /* stage('Security-Image-Scanner') {
        build 'SECURITY-IMAGE-SCANNER-AQUAMICROSCANNER'
    } */
    
    stage('Pull-Image-Server') {
         sh "docker-compose down"
         sh "docker-compose up -d"
    }
    
    /* stage('DAST') {
        build 'SECURITY-DAST-OWASP_ZAP'
    } */
}
