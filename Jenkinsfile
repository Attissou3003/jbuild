def pipelineContext = [:]
node {

    def registryProjet = 'registry.gitlab.com/attissou3003/presentations-jenkins'

    // Charge la version depuis un fichier ou une variable d'environnement
    def versionFile = 'version.txt' // Par exemple, un fichier version.txt dans le repository
    def version = ''
    
    // Si un fichier version.txt existe, on le lit, sinon on utilise une version par défaut
    if (fileExists(versionFile)) {
        version = readFile(versionFile).trim() // On lit la version depuis ce fichier
    } else {
        // Si pas de fichier version.txt, utilisez une version par défaut ou l'ID du build
        version = "default-version-${env.BUILD_ID}"
    }

    // Crée l'image avec la version obtenue
    def IMAGE = "${registryProjet}:${version}"
    
    echo "IMAGE = $IMAGE"
    
    stage('Clone') {
        checkout scm
    }

    def img = stage('Build') {
        docker.build("$IMAGE", '.')
    }

    stage('Run') {
        img.withRun("--name run-${version} -p 86:86") { c ->
            sh 'curl localhost'
        }
    }

    stage('Push') {
        docker.withRegistry('https://registry.gitlab.com', 'reg1') {
            img.push 'latest'
            img.push() // Pousse aussi la version spécifique
        }
    }
}
