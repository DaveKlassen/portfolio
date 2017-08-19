pipeline {
  agent any
  stages {
    stage('Init') {
      steps {
        echo 'Init'
      }
    }
    stage('Build') {
      steps {
        echo 'Compile'
      }
    }
    stage('Deploy') {
      steps {
        echo 'Deploy'
        script {
          def userInput = true
          def didTimeout = false
          try {
            timeout(time: 20, unit: 'SECONDS') { // change to a convenient timeout for you
            userInput = input(
              id: 'DeployToProd',
              message: 'Would you like to deploy this to production?',
              ok: 'Vote',
              parameters: [
                [$class: 'ChoiceParameterDefinition', choices: 'Deploy\nAbort\nUndecided', name: 'Please indicate your decision.', description: 'Someone must approve production deployments']
              ]
            )
          }
        } catch(err) { // timeout reached or input false
        echo "Errors: [${err}]"
        def user = err.getCauses()[0].getUser()
        echo "User: [${user}]"
        didTimeout = true
        currentBuild.result = 'UNSTABLE'
        if('SYSTEM' == user.toString()) { // SYSTEM means timeout.
        didTimeout = true
      } else {
        userInput = false
        echo "Aborted by: [${user}]"
      }
    }
    
    echo "userInput [${userInput}]"
    echo "didTimeout [${didTimeout}]"
    
    if (userInput == "Deploy") {
      sh 'echo \'Deploying...\''
    } else if ( (!userInput) || (userInput == "Abort") ) {
      echo "The user decided to abort deployment..."
      currentBuild.result = 'ABORTED'
    } else if ( (didTimeout) || (userInput == "Undecided") )  {
      echo "A user was undecided about a production deployment."
      currentBuild.result = 'NOT_BUILT'
    } else {
      echo "The decision reached, was not expected."
      currentBuild.result = 'UNSTABLE'
    }
    
    echo "Build Result : [${currentBuild.result.toString()}]"
  }
  
}
}
}
}