**Helm install jenkins**

    helm install jenkins 

**Docker in docker (docker not found) with kubectl and helm** 

    def POD_LABEL = "worker-${UUID.randomUUID().toString()}"

    podTemplate(label: POD_LABEL, containers: [
      containerTemplate(name: 'docker', image: 'docker', command: 'cat', ttyEnabled: true),
      containerTemplate(name: 'kubectl', image: 'lachlanevenson/k8s-kubectl:v1.8.8', command: 'cat', ttyEnabled: true),
      containerTemplate(name: 'helm', image: 'alpine/helm:latest', command: 'cat', ttyEnabled: true)
    ],
    volumes: [
      hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock')
    ]) {

      def String GIT_HASH

      node(POD_LABEL) {
        echo "Will deploy branch ${params.BRANCH}"
        stage('Build Docker image') {
            git branch: "master",
                credentialsId: 'Jenkins-gitlab',
                url: 'HERE_GIT_REMOTE_URL'
            sshagent (credentials: ['Jenkins-gitlab']) {            
                GIT_HASH = sh(script: "git ls-remote HERE_GIT_REMOTE_URL ${params.BRANCH} | awk '{ print \$1}'", returnStdout: true).trim()
            }        
            container('docker') {
                  docker.withRegistry('ECR_URL', 'ecr:eu-west-3:JENKINS_CREDENTIAL_ID') {
                    sh "docker build --build-arg BRANCH=${params.BRANCH} --no-cache	--network host ./Docker/php-front -t front:${GIT_HASH}"
                    docker.image("front:${GIT_HASH}").push()
                  }
            }
        }
        stage('Run helm') {
          container('helm') {
            sh "sed -i 's/appVersion:.*/appVersion: ${GIT_HASH}/g' ./K8s/Helm/front/Chart.yaml"
            sh "helm upgrade -i front ./K8s/Helm/front"
          }
        }
      }
    }
**PUsefull plugins**
    - agent ssh
    - Amazon ecr
**Push to ECR**

Create credentials in jenkins from aws : [https://console.aws.amazon.com/iam/home#/users](https://console.aws.amazon.com/iam/home#/users)

          docker.withRegistry('RegistryUrl', 'ecr:eu-west-3:Jenkins-credential-id') {
            docker.image('imagename:tag').push('tag')
          }
