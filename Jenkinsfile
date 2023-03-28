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
        volumeMounts:
        - name: shared-storage
          mountPath: /mnt
      - name: cloud-sdk
        image: google/cloud-sdk
      command:
        - sleep
        args:
        - 9999999
        volumeMounts:
        - name: shared-storage
          mountPath: /mnt
        - name: google-cloud-key
          mountPath: /var/secrets/google
        env:
        - name: GOOGLE_APPLICATION_CREDENTIALS
          value: /var/secrets/google/devops0001-94137bc9bb28.json
      volumes:
      - name: shared-storage
        persistentVolumeClaim:
          claimName: jenkins-pv-claim
      - name: google-cloud-key
        secret:
          secretName: sdk-key
    restartPolicy: Never
''') {
  node(POD_LABEL) {
      git branch: 'main', url: 'https://github.com/vishalshende83/week9.git'
      stage('Start a gradle project') {
      container('gradle') {
        stage('Smoke Test Calculator') {
            sh '''
        echo 'Smoke Test Calculator in Staging'
                curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
        chmod +x ./kubectl
        chmod +x gradlew
        ./kubectl apply -f calculator.yaml -n staging
        ./kubectl apply -f hazelcast.yaml -n staging
        sleep 30
        ./gradlew smokeTest -Dcalculator.url=http://calculator-service.staging.svc.cluster.local:8080
        '''
        }
      }
      }
      stage('Deploy to Cloud') {
      container('cloud-sdk') {
        stage('Deploy to GKE') {
          sh '''
         echo 'Deploying to GKE'
         gcloud auth login --cred-file=$GOOGLE_APPLICATION_CREDENTIALS
         gcloud config set project devops0001
         gcloud container clusters get-credentials hello-cluster --region us-east1 --project devops0001
         echo 'namespaces in the prod environment'
         kubectl get ns
         kubectl apply -f calculator.yaml
         kubectl apply -f hazelcast.yaml
         '''
        }
      }
      }
  }
}
