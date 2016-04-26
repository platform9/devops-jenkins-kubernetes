node('docker') {
stage 'Checkout SCM'
checkout scm
stage 'Build Docker image'
def img = docker.build("pf9dockerhub/pf9-demo:${env.BUILD_TAG}")
docker.withRegistry('', 'docker-registry-login'){
  img.push();
  img.push('latest');
}

stage 'Deploy to cluster'
  //Show Kubernetes serviceAccount Secret details
  //sh("ls -la /var/run/secrets/kubernetes.io/serviceaccount")
  //sh("cat /var/run/secrets/kubernetes.io/serviceaccount/token")
  //sh("cat /var/run/secrets/kubernetes.io/serviceaccount/ca.crt")
  //sh("cat /var/run/secrets/kubernetes.io/serviceaccount/namespace")

  //Set Kubernetes config
  sh("kubectl config set-credentials jenkins-build --token=/var/run/secrets/kubernetes.io/serviceaccount/token")
  sh("kubectl config set-cluster internal1 --server=http://kube-api.platform9.sys:8080 --insecure-skip-tls-verify=true")
  sh("kubectl config set-context default --user=jenkins-build --namespace=default --cluster=internal1")
  sh("kubectl config use-context default")

  //View Kubernetes config and test
  //sh("kubectl config view")
  sh("kubectl get nodes")

  sh("kubectl rollingupdate pf9-demo --image=${img.id} --update-period=1s")
}
