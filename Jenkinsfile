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
        stage('Get the exiting PODs') {
		    sh '''
        echo 'Existing PODs for Staging'
				curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
        chmod +x ./kubectl
        ./kubectl get pods -n staging
        '''
       } 
       stage('Rollover Updates for Calculator ') {
		    sh '''
        echo 'Deploy Calculator : Replica Count - 3'
				./kubectl apply -f calculator.yaml -n staging
        sleep 20
        '''
       }
       stage('PODs after rollver Update') {
		    sh '''
        echo 'PODs for Staging after rollover updates'
				./kubectl get pods -n staging
        '''
       }
     }
    }        
  }
} 