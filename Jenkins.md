**Helm install jenkins**

    helm install jenkins 

**Docker in docker (docker not found)** 

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
                credentialsId: 'jenkins-credential',
                url: 'git-url-here'
            container('docker') {
                sh "docker build --network host ./Docker/php"
            }
        }
      }
    }

**Push to ECR**

Create credentials in jenkins from aws : [https://console.aws.amazon.com/iam/home#/users](https://console.aws.amazon.com/iam/home#/users)

          docker.withRegistry('RegistryUrl', 'ecr:eu-west-3:Jenkins-credential-id') {
            docker.image('imagename:tag').push('tag')
          }
