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
      git branch: 'rollingUpdates', url: 'https://github.com/vishalshende83/week9.git'
      container('gradle') {
        stage('Existing PODs Staging') {
		sh '''
        echo 'Get the existing PODs Staging'
		curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
        chmod +x ./kubectl
        ./kubectl get pods -n staging
        '''
       } 
	   
	   stage('Rollover Updates') {
		sh '''
        echo 'Deploy Rolling Updates for Calculator. Replica count is 3'
        ./kubectl apply -f calculator.yaml -n staging
        sleep 20
		    '''
       }
	   
	   stage('PODs Staging after Rolling Updates') {
		sh '''
        echo 'Get the PODs in Staging after rolling Updated'
		./kubectl get pods -n staging
        '''
       }
     }
    }        
  }
} 