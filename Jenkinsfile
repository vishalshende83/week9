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
                ./kubectl apply -f calculator.yaml
                ./kubectl apply -f hazelcast.yaml
         '''
       } 

        stage("Run Acceptance Test") {
		sh '''
                echo 'Run Acceptance Test'
				chmod +x gradlew
                ./gradlew acceptanceTest -Dcalculator.url=http://calculator-service:8080
		  '''
		  publishHTML(target: [
                reportDir: 'build/reports/tests/acceptanceTest',
                reportFiles: 'index.html',
                reportName: "Calculator Test Coverage"
				])
            }
		
		stage("Test using Curl") {
		sh '''
                echo 'Test using Curl'
				test $(curl calculator-service:8080/sum?a=6\\&b=2) -eq 8 && echo 'pass' || 'fail'
				test $(curl calculator-service:8080/div?a=6\\&b=2) -eq 3 && echo 'pass' || 'fail'
				test $(curl calculator-service:8080/div?a=6\\&b=0) -eq Infinity && echo 'pass' || 'fail'
         '''
            }
		
		
      }
    }        

  }
} 