pipeline {
    
    environment {
      dockerimagename = "rikichrisoliva/linux_tweet_app-image"
      dockerimagenamn = "rikichrisoliva/nginx"
      dockerImage = ""
    }
    
	agent any
	
	stages {
  stage('Checkout Source') {
    steps {
      git 'https://github.com/rikichrisoliva/linux_tweet_app.git'
    }
  }

  stage('Build Image') {
    steps {
      script {
        dockerImage = docker.build(dockerimagename, "-f ./linux_tweet_app/Dockerfile ./linux_tweet_app/.")  
        dockerImage = docker.build(dockerimagenamn, "-f ./nginx/Dockerfile ./nginx/.")
      }
    }
  }

  stage('Pushing Image') {
    environment {
             registryCredential = 'dockerhublogin'
    }
    steps {
      script {
        docker.withRegistry('https://registry-1.docker.io/v2/', registryCredential) {
          dockerImage.push("latest")    
        }
      }
    }
  }

  stage('Run Pod') {
    steps {
      sshPublisher(publishers: [sshPublisherDesc(configName: 'kubernetes-host-1', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'kubectl apply -f ./linux_tweet_app/linux_tweet_app.yaml', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: 'linux_tweet_app', remoteDirectorySDF: false, removePrefix: 'linux_tweet_app', sourceFiles: 'linux_tweet_app/*')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
      sshPublisher(publishers: [sshPublisherDesc(configName: 'kubernetes-host-1', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'kubectl apply -f ./nginx/ssl.yaml; kubectl apply -f ./nginx/myproxy.yaml', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: 'nginx', remoteDirectorySDF: false, removePrefix: 'nginx', sourceFiles: 'nginx/*')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
    }
  }


}

}
