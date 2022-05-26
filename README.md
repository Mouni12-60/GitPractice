pipeline {

    
  agent {
      
    node {
        label 'fi000verso14'
        customWorkspace '/data/home/jenkins/workspace/a2-dev/A2_CM_INFRA/cm-workspace-dev'
         }
        }

    environment {
        JAVA_HOME = '/data/home/jenkins/workspace/a2-dev/A2_CM_INFRA/java1.8.0_171/jdk1.8.0_171'
        ANT_HOME = '/data/home/jenkins/workspace/a2-dev/A2_CM_INFRA/apache-ant-1.10.5'
        PATH = "${PATH}:${ANT_HOME}/bin:${JAVA_HOME}/bin"
                }        

   stages {
               /* stage('notifying slack') {
         steps {
echo 'Notifying build'
script {
notifyBuild()
}
}
} */

        stage('Initialize') { 
            steps { 
                    echo "$JAVA_HOME"
                    echo "$ANT_HOME"
                    echo "$PATH"
                    sh "bash"
                    sh "ant -version"
                    sh "java -version"
                    sh "ant init"
                  }
                            }     

        stage('prepare_deploy') { 
            steps { 
                    sh "ant -Dbranch_name=${branch_to_build} -Dbuild_type_temp_var=${build_content} -Dbuild_target_temp_var=${build_to_be_done_for} -Dhost.choice=${target_environment} prepare_deploy"
                  }
                                                 }
                                                 
        stage('build_checkout') { 
            steps { 
                    sh "ant build_checkout"
                  }
                                }
       
                                              
                          
                                                 stage('set_build_number') { 
            steps { 
            script {
                    def props = readProperties  file: "tmp/temp.properties"
                    env.package_name=props.'package.name'
                    currentBuild.displayName = "${package_name}"
        }
                            echo "${package_name}"
            }
        }
        
/*
                   stage('create_binaries') { 
            steps { 
                    sh "ant create_binaries"
                  }
                                }     */
                                                                                                                                                
    
      
                                               
                                             
        stage('deploy+') { 
            steps { 
                    sh "ant deploy+"
                  }
                                }  
                                
    } 

            
            
        post {
success {
echo 'Notifying Slack about Success'
script {
notifySucess()
}
}
failure {
echo 'Notifying Slack about FAILURE'
script {
notifyFailure()
}
}
}
            }

         
         def notifySlack(overrides = [:]) {
conf = [
baseUrl: 'https://teliafin.slack.com/services/hooks/jenkins-ci/',
channel: '#alpha2-jenkins',
tokenCredentialId: 'New-channel'
] << overrides
slackSend conf
}

def notifySucess() {
    def tenvironment = 'tenv'
    if ("${target_environment}" =='1') {
        tenvironment="A2UAT"
    }
    else if ("${target_environment}" =='2') {
        tenvironment="A2CM"
    }
    else if ("${target_environment}" =='3') {
        tenvironment="A2SID1"
    }
    else if ("${target_environment}" =='4') {
        tenvironment="A2SID2"
    }
    else if ("${target_environment}" =='5') {
        tenvironment="A2BUR12"
    }
    else if ("${target_environment}" =='6') {
        tenvironment="A2ST"
    }
    else if ("${target_environment}" =='7') {
        tenvironment="A2SIT"
    }
    else if ("${target_environment}" =='8') {
        tenvironment="A2BUWAR"
    }
    else if ("${target_environment}" =='9') {
        tenvironment="A2BUD1"
    }
    else if ("${target_environment}" =='10') {
        tenvironment="A2MX"
    }
    else if ("${target_environment}" =='11') {
        tenvironment="A2BUD2B"
    }
    else if ("${target_environment}" =='12') {
        tenvironment="A2CT"
    }
    else if ("${target_environment}" =='13') {
        tenvironment="A2TRAIN"
    }
    else if ("${target_environment}" =='14') {
        tenvironment="A2M"
    }
    else if ("${target_environment}" =='15') {
        tenvironment="CRMP"
    }
     else if ("${target_environment}" =='16') {
        tenvironment="A2BUD2"
    }
         def buildcontent = 'bc'
    if ("${build_content}" =='f') {
        build_content="file"
    }
    else if ("${build_content}" =='m') {
        build_content="module"
    }
     def jfrog = 'https://artifactory.verso.sonera.fi/ant_oracle-ebs_alpha2/test/'
     def jfrog_url = 'https://artifactory.verso.sonera.fi/ui/repos/tree/General/ant_oracle-ebs_alpha2/test/'
conf = [
message: """
Build and Deployment job is success and logs generated for the given branch:
            #BUILD_TRIGGER_BY:# ${currentBuild.getBuildCauses()[0].userId}
			#Branch:# ${branch_to_build}
	    	#Build_type:# ${build_content}
			#TargetEnv:# ${tenvironment}
			#BuildURL:# ${env.BUILD_URL}console
		    #Package name:#	${package_name}
		    #DownloadLogs:# ${jfrog}${package_name}.tar.gz
		    #jfrogbinaries_url:# ${jfrog_url}${package_name}.tar.gz
            """.stripIndent().replace('%2F', '/').trim(),
        color: 'good'
]
notifySlack conf
}
def notifyFailure() {
     def tenvironment = 'tenv'
    if ("${target_environment}" =='1') {
        tenvironment="A2UAT"
    }
    else if ("${target_environment}" =='2') {
        tenvironment="A2CM"
    }
    else if ("${target_environment}" =='3') {
        tenvironment="A2SID1"
    }
    else if ("${target_environment}" =='4') {
        tenvironment="A2SID2"
    }
    else if ("${target_environment}" =='5') {
        tenvironment="A2BUR12"
    }
    else if ("${target_environment}" =='6') {
        tenvironment="A2ST"
    }
    else if ("${target_environment}" =='7') {
        tenvironment="A2SIT"
    }
    else if ("${target_environment}" =='8') {
        tenvironment="A2BUWAR"
    }
    else if ("${target_environment}" =='9') {
        tenvironment="A2BUD1"
    }
    else if ("${target_environment}" =='10') {
        tenvironment="A2MX"
    }
    else if ("${target_environment}" =='11') {
        tenvironment="A2BUD2B"
    }
    else if ("${target_environment}" =='12') {
        tenvironment="A2CT"
    }
    else if ("${target_environment}" =='13') {
        tenvironment="A2TRAIN"
    }
    else if ("${target_environment}" =='14') {
        tenvironment="A2M"
    }
    else if ("${target_environment}" =='15') {
        tenvironment="CRMP"
    }
    else if ("${target_environment}" =='16') {
        tenvironment="A2BUD2"
    }
         def buildcontent = 'bc'
    if ("${build_content}" =='f') {
        build_content="file"
    }
    else if ("${build_content}" =='m') {
        build_content="module"
    }
 conf = [
message: """
Build and deployment job is failed:
        	#BUILD_TRIGGER_BY:# ${currentBuild.getBuildCauses()[0].userId}
        	#Branch:# ${branch_to_build}
	    	#Build_type:# ${build_content}
			#TargetEnv:# ${tenvironment}
			#BuildURL:# ${env.BUILD_URL}console
		             """.stripIndent().replace('%2F', '/').trim(),
        color: '#FF0000'
]
notifySlack conf
}
def notifyBuild() {
    def t_environment = 'tenv'
    if ("${target_environment}" =='1') {
        tenvironment="A2UAT"
    }
    else if ("${target_environment}" =='2') {
        tenvironment="A2CM"
    }
    else if ("${target_environment}" =='3') {
        tenvironment="A2SID1"
    }
    else if ("${target_environment}" =='4') {
        tenvironment="A2SID2"
    }
    else if ("${target_environment}" =='5') {
        tenvironment="A2BUR12"
    }
    else if ("${target_environment}" =='6') {
        tenvironment="A2ST"
    }
    else if ("${target_environment}" =='7') {
        tenvironment="A2SIT"
    }
    else if ("${target_environment}" =='8') {
        tenvironment="A2BUWAR"
    }
    else if ("${target_environment}" =='9') {
        tenvironment="A2BUD1"
    }
    else if ("${target_environment}" =='10') {
        tenvironment="A2MX"
    }
    else if ("${target_environment}" =='11') {
        tenvironment="A2BUD2B"
    }
    else if ("${target_environment}" =='12') {
        tenvironment="A2CT"
    }
    else if ("${target_environment}" =='13') {
        tenvironment="A2TRAIN"
    }
    else if ("${target_environment}" =='14') {
        tenvironment="A2M"
    }
    else if ("${target_environment}" =='15') {
        tenvironment="CRMP"
    }
        else if ("${target_environment}" =='16') {
        tenvironment="A2BUD2"
    }
      def buildcontent = 'bcb'
    if ("${build_content}" =='f') {
        buildcontent="file"
    }
    else if ("${build_content}" =='m') {
        buildcontent="module"
    }
conf = [
message: """
Build and deployment job started:
            #BUILD_TRIGGER_BY:# ${currentBuild.getBuildCauses()[0].userId}
      		#Branch:# ${branch_to_build}
	    	#Build_type:# ${buildcontent}
			#TargetEnv:# ${tenvironment}
			#BuildURL:# ${env.BUILD_URL}console
            """.stripIndent().replace('%2F', '/').trim(),
        color: 'good'
]
notifySlack conf
}

