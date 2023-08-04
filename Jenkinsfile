def getCommitDetails() {    
    def commit_id = []
    for (changeLog in currentBuild.changeSets) {      
      for (entry in changeLog.items) {
          echo "\n    Author: " + entry.getAuthor()
          echo "\n    Author Email: " + entry.getAuthorEmail()
          echo "\n    Commit ID : " + entry.getCommitId()
          commit_id.add(entry.getCommitId())         
          
              }
     }       
   return commit_id
     }

node {
    def scmVars
    def customImage
    def to_list
    
    try {
        stage('Checkout SCM') {
            scmVars = checkout([$class: 'GitSCM', branches: [[name: "${GIT_BRANCH}"]],
            	doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], 
            	userRemoteConfigs: [[credentialsId: 'asha personal creds', url: 'https://github.com/ashaikk/test.git']]])
        }
        currentBuild.result = 'SUCCESS'
    } catch (error) {
        currentBuild.result = 'FAILURE'
                    }
        stage('Post-build Notification') {
            if(currentBuild.result == 'FAILURE'){
                    to_list = 'asha.shaik@blinqnetworks.com'
                }
            
            commits = getCommitDetails()
            for (item in commits)
            {                
                author = sh (script: 'git --no-pager show -s --format=\'%ae\' ${item}',returnStdout: true).trim()                
                if (to_list == null)
                    to_list = author
                else if (!(to_list.contains(author)))
                    to_list = to_list + "," + author

            } 

            emailext attachLog: true,
            body: "${env.BUILD_URL} has result ${currentBuild.result}",
            compressLog: true,
            //recipientProviders: [developers(),requestor(),upstreamDevelopers()],
            subject: "Status of pipeline: ${currentBuild.fullDisplayName}",
            to:to_list
        }
    }

