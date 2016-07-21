node('docker') {
stage 'Checkout SCM'
checkout scm
stage 'Build Docker image'
def img = docker.build("pf9dockerhub/pf9-demo:${env.BUILD_TAG}")
docker.withRegistry('', 'docker-registry-login'){
  img.push('latest');
}

stage 'Deploy to cluster'
  //Set Kubernetes config
  sh("kubectl config set-credentials jenkins-build --token=`cat /var/run/secrets/kubernetes.io/serviceaccount/token`")
  sh("kubectl config set-cluster internal1 --server=https://kubernetes --certificate-authority=/var/run/secrets/kubernetes.io/serviceaccount/ca.crt")
  sh("kubectl config set-context default --user=jenkins-build --namespace=`cat /var/run/secrets/kubernetes.io/serviceaccount/namespace`  --cluster=internal1")
  sh("kubectl config use-context default")

  sh("kubectl rollingupdate pf9-demo --image=${img.id} --update-period=1s")
}
