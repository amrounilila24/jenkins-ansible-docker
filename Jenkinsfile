node {

    def registryProjet = "registry.gitlab.com/amrounilila24/pres"
    def IMAGE = "${registryProjet}:compil-${env.BUILD_ID}"
    def img

    stage('Build - Clone') {
        git 'https://github.com/amrounilila24/jenkins-ansible-docker.git'
    }

    stage('Build - Maven package') {
        sh 'mvn clean package'
    }

    stage('Build - Docker Image') {
        img = docker.build(IMAGE, '.')
    }

    stage('Build - Test') {
        img.withRun("--name run-${BUILD_ID} -p 8081:8080") {
            sh 'docker ps'
            sh 'sleep 30'
            sh 'curl http://localhost:8081'
        }
    }

    stage('Build - Push') {
        docker.withRegistry('https://registry.gitlab.com', 'reg1') {
            img.push('latest')
            img.push("compil-${env.BUILD_ID}")
        }
    }

    stage('Deploy - Clone') {
        git 'https://github.com/amrounilila24/jenkins-ansible-docker.git'
    }

    stage('Deploy - End') {
        ansiblePlaybook(
            colorized: true,
            become: true,
            playbook: 'playbook.yml',
            inventory: "${HOST},",
            extras: "--extra-vars image=${IMAGE}"
        )
    }
}
