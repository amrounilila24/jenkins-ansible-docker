node {

    // =====================
    // VARIABLES
    // =====================
    def registryProjet = "registry.gitlab.com/amrounilila24/pres"
    def IMAGE = "${registryProjet}:compil-${env.BUILD_ID}"
    def img

    // =====================
    // BUILD APP (DOCKER)
    // =====================
    stage('Build - Clone App') {
        dir('build') {
            git branch: 'main',
                url: 'https://gitlab.com/amrounilila24/pres.git'
        }
    }

    stage('Build - Docker Image') {
        dir('build') {
            img = docker.build("${IMAGE}")
        }
    }

    stage('Build - Test') {
        img.withRun("--name run-${env.BUILD_ID} -p 8081:8080") {
            sh 'docker ps'
            sh 'sleep 10'
            sh 'curl http://localhost:8081 || true'
        }
    }

    stage('Build - Push') {
        docker.withRegistry('https://registry.gitlab.com', 'reg1') {
            img.push()
            img.push('latest')
        }
    }

    // =====================
    // DEPLOY (ANSIBLE)
    // =====================
    stage('Deploy - Clone') {
        dir('deploy') {
            git branch: 'master',
                url: 'https://github.com/amrounilila24/jenkins-ansible-docker.git'
        }
    }

    stage('Deploy - End') {
        dir('deploy') {
            ansiblePlaybook(
                colorized: true,
                become: true,
                playbook: 'playbook.yml',
                inventory: "${HOST},",
                extras: "--extra-vars \"image=${IMAGE}\""
            )
        }
    }
}
