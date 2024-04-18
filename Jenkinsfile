  def label = "eosagent"
  def env = "main"
  podTemplate(label: label, yaml: """
  apiVersion: v1
  kind: Pod
  metadata:
    labels:
      name: build
  spec:
    serviceAccount: jenkins-admin
    containers:
    - name: build
      image: dpthub/eos-jenkins-agent-base:latest
      command:
      - cat
      tty: true
      resources:
      requests:
        memory: "256Mi"  # Request 256 megabytes of memory
        cpu: "100m"      # Request 100 milliCPU (0.1 CPU core)
      limits:
        memory: "1024Mi"  # Limit memory usage to 512 megabytes
        cpu: "500m"      # Limit CPU usage to 200 milliCPU (0.2 CPU core)
  """
  ) {
      node (label) {

          stage ('Checkout SCM'){
            git credentialsId: 'git', url: 'https://github.com/rajtaldevop1/eos-cloud-config-api-deployment.git', branch:  "${env}"
          }

          stage ('Helm Chart') {
            container('build') {
                withCredentials([usernamePassword(credentialsId: 'jfrog', usernameVariable: 'username', passwordVariable: 'password')]) {
                      sh '/usr/local/bin/helm repo add eos-helm-local  https://rajdev.jfrog.io/artifactory/eos-helm-local --username $username --password $password'
                      sh "/usr/local/bin/helm repo update"
                      sh "/usr/local/bin/helm upgrade  --install --force cloud-config-api  --namespace ${env} -f values.yaml eos-helm-local/cloud-config-api"
                      sh "/usr/local/bin/helm list -a --namespace ${env}"
                      sh "rm -rf values.yaml"
              }
          }
          }
      }
  }