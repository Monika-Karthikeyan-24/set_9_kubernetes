
pipeline {
    agent any

    stages {
        stage('Validate Kubernetes') {
            steps {
                bat 'kubectl version --client'
            }
        }

        stage('Create PVC and Pod') {
            steps {
                withCredentials([
                    file(credentialsId: 'kubeconfig-credentials',
                         variable: 'KUBECONFIG')
                ]) {
                    bat 'kubectl apply -f storage.yaml'
                }
            }
        }

        stage('Wait for Pod') {
            steps {
                withCredentials([
                    file(credentialsId: 'kubeconfig-credentials',
                         variable: 'KUBECONFIG')
                ]) {
                    bat 'kubectl wait --for=condition=Ready pod/storage-pod --timeout=180s'
                }
            }
        }

        stage('Verify Storage') {
            steps {
                withCredentials([
                    file(credentialsId: 'kubeconfig-credentials',
                         variable: 'KUBECONFIG')
                ]) {
                    bat 'kubectl get pvc my-pvc'
                    bat 'kubectl get pod storage-pod'
                    bat 'kubectl exec storage-pod -- sh -c "echo Persistent storage works > /usr/share/nginx/html/storage/test.txt"'
                    bat 'kubectl exec storage-pod -- cat /usr/share/nginx/html/storage/test.txt'
                }
            }
        }
    }
}
