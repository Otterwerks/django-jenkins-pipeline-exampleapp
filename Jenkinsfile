def label = "worker-${UUID.randomUUID().toString()}"
podTemplate(label: label, containers: [
    containerTemplate(name: 'jnlp', image: 'tomdierckx/jenkins-arm-slave-jnlp:0.0.2', args: '${computer.jnlpmac} ${computer.name}'),
    containerTemplate(name: 'python3', image: 'arm32v7/python:3.6.5', ttyEnabled: true, command: 'cat'),
    containerTemplate(name: 'docker', image: 'docker', command: 'cat', ttyEnabled: true),
    ],
volumes: [
    hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock')
]) {

    node(label) {
        stage('Build') {
            git url:'https://github.com/Otterwerks/django-jenkins-pipeline-exampleapp.git'
            container('python3') {
                stage('Running tests') {
                    sh 'pip install -r requirements.txt'
                    sh 'python manage.py test'
                }
            }
            container('docker') {
                stage('Building docker image') {
                    sh 'docker build -t otterwerks/python-django-ci-testapp:${BUILD_NUMBER} .'
                    withDockerRegistry([ credentialsId: "dockerhub", url: "" ]) {
                        sh 'docker push otterwerks/python-django-ci-testapp:${BUILD_NUMBER}'
                    }
                }
            }
        }
    }   
}
