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
      git branch: 'imageUpdate', url: 'https://github.com/vishalshende83/week9.git'
      container('gradle') {
        stage('Test Addition and Division for image: dlambrig/hello-kaniko:0.5') {
		sh '''
        echo 'Addition should pass & Division should fail'
		curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
        chmod +x ./kubectl
        '''
		try {
              sh '''
              test $(curl calculator-service.staging.svc.cluster.local:8080/sum?a=4\\&b=2) -eq 6 && echo 'pass' || echo 'fail'
			  test $(curl calculator-service.staging.svc.cluster.local:8080/sum?a=4\\&b=4) -eq 8 && echo 'pass' || echo 'fail'
              '''
            }
            catch (Exception e) {
               echo "Test for addition failed"
            }
		try {
              sh '''
              test $(curl calculator-service.staging.svc.cluster.local:8080/div?a=4\\&b=2) -eq 2 && echo 'pass' || echo 'fail'
			  test $(curl calculator-service.staging.svc.cluster.local:8080/div?a=8\\&b=2) -eq 4 && echo 'pass' || echo 'fail'
              '''
            }
            catch (Exception e) {
               echo "Test for division failed"
            }
       } 
	   
	   stage('Deploy calculator') {
		sh '''
        echo 'Deploying Calculator for image: dlambrig/week8:1.1'
		./kubectl apply -f calculator.yaml -n staging
		sleep 30
        '''
       }
	   
	   stage('Test Addition and Division for image: dlambrig/week8:1.1') {
		try {
              sh '''
              test $(curl calculator-service.staging.svc.cluster.local:8080/sum?a=4\\&b=2) -eq 6 && echo 'pass' || echo 'fail'
			  test $(curl calculator-service.staging.svc.cluster.local:8080/sum?a=4\\&b=4) -eq 8 && echo 'pass' || echo 'fail'
              '''
            }
            catch (Exception e) {
               echo "Test for addition failed"
            }
		try {
              sh '''
              test $(curl calculator-service.staging.svc.cluster.local:8080/div?a=4\\&b=2) -eq 2 && echo 'pass' || echo 'fail'
			  test $(curl calculator-service.staging.svc.cluster.local:8080/div?a=8\\&b=2) -eq 4 && echo 'pass' || echo 'fail'
              '''
            }
            catch (Exception e) {
               echo "Test for division failed"
            }
       }
     }
    }        
  }
} 