
def imageName = 'paulappz/movies-loader'
// def registry = 'https://registry.gbnlcicd.com'
def registry = '530364773324.dkr.ecr.eu-west-2.amazonaws.com' 
def region = 'eu-west-2'


node('workers'){
    stage('Checkout'){
        checkout scm
    }

    stage('Unit Tests'){
        def imageTest= docker.build("${imageName}-test", "-f Dockerfile.test .")
        imageTest.inside{
            sh "python test_main.py"
        }
    }

    stage('Build'){
       //def imageBuild = docker.build(imageName)
        sh "aws ecr get-login-password --region ${region} | docker login --username AWS --password-stdin ${registry}/${imageName}"
        sh "docker build --tag ${imageName}:${commitID()} ."
        sh " docker tag ${imageName}:${commitID()} ${registry}/${imageName}:${commitID()}"
    }
    
    stage('Push'){
     //  sh "aws ecr get-login-password --region ${region} | docker login --username AWS --password-stdin ${registry}/${imageName}"
     
     //  sh "  docker build -t ${imageName} . "
     //  sh " docker tag ${imageName}:${commitID()} ${registry}/${imageName}:${commitID()}"
       sh "docker push ${registry}/${imageName}:${commitID()}"

         //  imageBuild.push(commitID()) 
         //       if (env.BRANCH_NAME == 'develop') {
         //   imageBuild.push('develop')
         //        } 
    
}

    stage('Analyze'){
           def scannedImage = "${registry}/${imageName}:${commitID()} ${workspace}/Dockerfile"
           writeFile file: 'images', text: scannedImage
            anchore name: 'images'
    }
}

def commitID() {
    sh 'git rev-parse HEAD > .git/commitID'
    def commitID = readFile('.git/commitID').trim()
    sh 'rm .git/commitID'
    commitID
}

