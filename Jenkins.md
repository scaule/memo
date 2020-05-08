Docker in docker 

podTemplate(yaml: """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: docker
    image: docker:latest
    command: ['cat']
    tty: true
    volumeMounts:
    - name: dockersock
      mountPath: /var/run/docker.sock
  volumes:
  - name: dockersock
    hostPath:
      path: /var/run/docker.sock
"""
  ) {

  def image = "jenkins/jnlp-slave"
  node(POD_LABEL) {
    stage('Build Docker image') {
        git branch: 'master',
            credentialsId: '926e3fd5-4505-4633-b953-1aaf54307e8a',
            url: 'git@git.clever-age.net:easycash/infra-cloud.git'
        container('docker') {
            sh "docker build --network host ./Docker/php"
        }
    }
  }
}
