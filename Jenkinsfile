podTemplate(yaml: '''
    apiVersion: v1
    kind: Pod
    spec:
      containers:
      - name: gradle
        image: gradle:jdk8
        command:
        - sleep
        args:
        - 99d
      restartPolicy: Never
    ''') {
  node(POD_LABEL) {

  stage('Start a gradle project') {
      git branch: 'main', url: 'https://github.com/vishalshende83/week9.git'
      container('gradle') {
        stage('Start Calculator') {
		sh '''
                echo 'Start Calculator'
				curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
                chmod +x ./kubectl
                ./kubectl apply -f calculator.yaml -n staging
                ./kubectl apply -f hazelcast.yaml -n staging
         '''
       } 
     }
    }        

  }
} 