
def imageName = 'paulappz/movies-loader'
// def registry = 'https://registry.gbnlcicd.com'
def registry = '530364773324.dkr.ecr.eu-west-2.amazonaws.com' 
def region = 'eu-west-2'

environment{
        AWS_ACCESS_KEY_ID=credentials('aws_key_id')
        AWS_SECRET_ACCESS_KEY=credentials('aws_key_secret')
        AWS_DEFAULT_REGION="eu-west-2"
    }


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
       def imageBuild = docker.build(imageName)
    }
    
    stage('Push'){
       sh "aws ecr get-login-password --region ${region} | docker login --username AWS --password-stdin ${registry}/${imageName}"
        // docker.build(imageName)
       sh "  docker build -t ${imageName} . "
       sh " docker tag ${imageName}:latest ${registry}/${imageName}:latest"
       sh "docker push ${registry}/${imageName}:latest"

        //  imageBuild.push(commitID()) 
         //       if (env.BRANCH_NAME == 'develop') {
         //   imageBuild.push('develop')
           //        } 
    
}

    stage('Analyze'){
        //    def scannedImage = "${registry}/${imageName}:${commitID()} ${workspace}/Dockerfile"
        //    writeFile file: 'images', text: scannedImage
        //    anchore name: 'images'
    }
}

def commitID() {
    sh 'git rev-parse HEAD > .git/commitID'
    def commitID = readFile('.git/commitID').trim()
    sh 'rm .git/commitID'
    commitID
}

