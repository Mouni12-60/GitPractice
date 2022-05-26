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
----------------------------------------------------------------------------------------------------------------------
<?xml version="1.0"?>
<project name="CM" default="targets" basedir=".">
<!-- This is a build file slightly customized for https://versojenkins.eficode.com/ build -->
<!--Secure jsch-0.1.xx.jar on apache-ant-1.xx.x/bin path http://www.jcraft.com/jsch/-->

	<taskdef resource="net/sf/antcontrib/antcontrib.properties">
  		<classpath>
    		<pathelement location="./extra_antlib/ant-contrib-1.0b3/ant-contrib-1.0b3.jar"/>
  		</classpath>
	</taskdef>		
	<taskdef resource="net/sf/antcontrib/antlib.xml">
  		<classpath>
    		<pathelement location="./extra_antlib/ant-contrib-1.0b3/ant-contrib-1.0b3.jar"/>
  		</classpath>
	</taskdef>
	<taskdef name="unset" classname="ise.antelope.tasks.Unset">
  		<classpath>
    		<pathelement location="./extra_antlib/antelopetasks-3.2.10/antelopetasks-3.2.10.jar"/>
  		</classpath>
	</taskdef>	<!--http://antelope.tigris.org/nonav/docs/manual/bk03ch08.html, http://www.java2s.com/Code/Jar/a/Downloadantelopetasks3210jar.htm | This is included to enable use of unset task-->

<taskdef name="sshexec" 
classname="org.apache.tools.ant.taskdefs.optional.ssh.SSHExec" 
classpathref="jsch.class.path" />
<path id="jsch.class.path">
 <pathelement location="./ant-lib/jsch-0.1.55/jsch-0.1.55.jar" />
</path>

  
<taskdef name="scp" 
classname="org.apache.tools.ant.taskdefs.optional.ssh.Scp" 
classpathref="jsch.class.path" />
<path id="jsch.class.path">
 <pathelement location="./ant-lib/./ant-jsch/ant-jsch.jar" />
</path>

  
  
  
  <taskdef resource="net/jtools/classloadertask/antlib.xml" classpath="./extra_antlib/ant-classloadertask-1.0-RC2/ant-classloadertask.jar"/>
    <classloader loader="project" classpath="./extra_antlib/jakarta.mail-1.6.5/jakarta.mail-1.6.5.jar">
      <classpath>
        <pathelement path="./extra_antlib/ant-javamail-1.10.1/activation-1.1.jar"/>
        <pathelement path="./extra_antlib/ant-javamail-1.10.1/activation-1.1.jar"/>
        <pathelement path="./extra_antlib/ant-javamail-1.10.1/activation-1.1.jar"/>
        <pathelement path="./extra_antlib/ant-javamail-1.10.1/activation-1.1.jar"/>
        <pathelement path="./extra_antlib/ant-javamail-1.10.1/activation-1.1.jar"/>
        <pathelement path="./extra_antlib/ant-javamail-1.10.1/activation-1.1.jar"/>
      </classpath>
    </classloader>
	
	<description>
        alpha2 configuration management tasks
    </description>

	<target name="targets">
<!--Refer https://misc.flogisoft.com/bash/tip_colors_and_formatting for shell echo formatting-->		
		<shellscript shell="bash">
		echo -ne "\n"		
		echo -e "\e[32m The targets for alpha2 installation are as follows.. \e[0m"
		echo -ne "\n"
<!-- workspace targets -->
		echo -e "\e[96m 'setup_build_workspace'\e[0m\e[93m creates new workspace \e[0m"
		echo -ne "\n"		
		echo -e "\e[96m 'clock'\e[0m\e[93m initiates clock variable to use in this build script \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'clean'\e[0m\e[93m deletes build workspace and temp space \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'init'\e[0m\e[93m creates build workspace and temp space \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'clean_build_workspace'\e[0m\e[93m deletes build workspace \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'init_build_workspace'\e[0m\e[93m creates build workspace \e[0m"
		echo -ne "\n"
<!-- git targets -->					
		echo -e "\e[96m 'git clone'\e[0m\e[93m clones Alpha2-EBS to build workspace \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'branch_chckout'\e[0m\e[93m checks out specified branch on the Alpha2-EBS \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'branch_checkout_master'\e[0m\e[93m checks out master branch on the Alpha2-EBS \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'branch_autotag'\e[0m\e[93m creates auto-tag to enable list down of affected files (delta) between current day and day before \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'list_affected_features'\e[0m\e[93m lists the affected files on a branch \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'list_affected_features_between_tags'\e[0m\e[93m creates list down of affected files (delta) between two specified tags \e[0m"
		echo -ne "\n"		
		echo -e "\e[96m 'list_affected_features_between_autotags'\e[0m\e[93m creates list down of affected files (delta) between two auto tags \e[0m"
		echo -ne "\n"
<!-- utility targets -->			
		echo -e "\e[96m 'check_status'\e[0m\e[93m enables completion of a requested process example: app_patch_installation \e[0m"
		echo -ne "\n"
<!-- move binary targets -->	
		echo -e "\e[96m 'scp_app_binary'\e[0m\e[93m moves code binary for application patch to selected host \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'scp_to_remote'\e[0m\e[93m enables move of log from selected host to local machine \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'scp_app_inst_log'\e[0m\e[93m master target for 'scp_app_binary_inst_log','scp_deliverycode_inst_log' \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'scp_app_binary_inst_log','scp_deliverycode_inst_log','scp_deliverycode_inst_log_sub_process'\e[0m\e[93m move application patch installation logs from selected host to local machine \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'scp_db_binary'\e[0m\e[93m moves code binary for db install/db patch to selected host \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'scp_db_inst_log', 'scp_db_binary_inst_log'\e[0m\e[93m moves db install / db patch installation logs from selected host to local machine \e[0m"
		echo -ne "\n"
<!-- prepare deploy targets -->			
		echo -e "\e[96m 'prepare_deploy'\e[0m\e[93m enables change builder to choose/create the install items/configurations on a specified branch \e[0m"
		echo -ne "\n"								
		echo -e "\e[96m 'prepare_deploy_between_tags'\e[0m\e[93m enables change builder to choose/create the install items/configurations on a specified branch (between two tags) \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'prepare_deploy_between_autotags'\e[0m\e[93m enables change builder to choose/create the install items/configurations on a specified branch (between two autotags) \e[0m"
		echo -ne "\n"				
		echo -e "\e[96m 'prepare_deploy_on_demand'\e[0m\e[93m enables change builder to choose/create the install items/configurations on a specified branch (for interested delivery codes) \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'build_branch'\e[0m\e[93m enables change builder to choose the branch to use for build \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'build_between_tags'\e[0m\e[93m enables change builder to choose the tags to build_between_tags \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'build_target'\e[0m\e[93m enables change builder to choose if the build/install has to be performed for test or production \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'build_type'\e[0m\e[93m enables change builder to choose if the build has to be performed for file only OR for the complete delivery code containing the file \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'set_buildnumber'\e[0m\e[93m auto-generates build number for application patch \e[0m"
		echo -ne "\n"		
		echo -e "\e[96m 'set_db_buildnumber'\e[0m\e[93m auto-generates build number for db patch \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'list_environments','print_name','set_variables'\e[0m\e[93m enables change builder to choose the target environment \e[0m"
		echo -ne "\n"		
		echo -e "\e[96m 'prepare_module_build_checkout_file'\e[0m\e[93m creates checkout file (to install complete delivery code containing the affected file) to perform checkout for application patch and/or db patch \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'prepare_file_build_checkout_file'\e[0m\e[93m creates checkout file (to install the affected file) to perform checkout for application patch and/or db patch \e[0m"
		echo -ne "\n"
<!-- install targets -->					
		echo -e "\e[96m 'app_build_checkout'\e[0m\e[93m performs checkout from Alpha2-EBS to build workspace to proceed with build creation for application patch \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'db_build_checkout'\e[0m\e[93m performs checkout from Alpha2-EBS to build workspace to proceed with build creation for db patch \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'jar_build_checkout'\e[0m\e[93m performs checkout from Alpha2-EBS to build workspace to proceed with build creation for jar patch \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'db_install_build_checkout'\e[0m\e[93m performs checkout from Alpha2-EBS to build workspace to proceed with build creation for db installation \e[0m"
		echo -ne "\n"		
<!-- prepare install targets -->
		echo -e "\e[96m 'prepare_db_installation'\e[0m\e[93m prepares for fresh db installation \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'db_patch_remove_existing_class_files', 'db_patch_remove_existing_class_files_sub_process'\e[0m\e[93m deletes existings build folder on selected host to enable new db patch installation \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'install_db'\e[0m\e[93m installs db on selected environment \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'install_db_patch'\e[0m\e[93m installs db patch on selected environment \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'prod_binaries'\e[0m\e[93m builds production binary and corresponding roll back binary for application patch, database patch and jar patch as applicable \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'create_rollback_binaries'\e[0m\e[93m enables 'prod_binaries' target to create rollback binaries \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'deploy+'\e[0m\e[93m installs the binaries on selected environment and proceeds for MD120_i673, MD120_f694_f695, middle_tier_restart, tomcat_restart, cm_restart and shared_pool_flush as applicable \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'deploy_and_park'\e[0m\e[93m installs the binaries on selected environment and proceeds for MD120_i673, MD120_f694_f695 \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'deploy_continue'\e[0m\e[93m used in sequence to 'deploy_and_park' to continue with middle_tier_restart, tomcat_restart, cm_restart and shared_pool_flush as applicable \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'build_checkout'\e[0m\e[93m performs checkout from Alpha2-EBS to build workspace. Master target for 'db_build_checkout', 'jar_build_checkout', 'app_build_checkout' as applicable \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'rollback_build_check'\e[0m\e[93m performs checkout from Alpha2-EBS to build workspace to create roll back build. Used by 'create_rollback_binaries' \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'setup_package'\e[0m\e[93m uses build.java to create index file for application patch \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'setup-partial-install'\e[0m\e[93m edits the installation mode on the index file created by 'setup_package' \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'make_package'\e[0m\e[93m creates the application patch binary \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'setup_db_package'\e[0m\e[93m uses DB_build.java to create index file for application patch \e[0m"
		echo -ne "\n"	
		echo -e "\e[96m 'make_db_package'\e[0m\e[93m creates the db install and db patch binary \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'create_jar'\e[0m\e[93m creates the JAR patch binary \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'compile'\e[0m\e[93m compiles source directory (build.java and DB_build.java) to create class files \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'JAR_File_Upload'\e[0m\e[93m uploads JAR binary to the configurator on Oracle EBS application \e[0m"
		echo -ne "\n"
<!-- install targets -->
		echo -e "\e[96m 'app_binary_unpack'\e[0m\e[93m unzips the application patch binary that was moved to selected server \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'app_binary_install'\e[0m\e[93m installs the application patch binary that was moved to selected server \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'check_app_binary_install_status'\e[0m\e[93m enables check of status of installation initiated by 'app_binary_install' \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'db_binary_unpack'\e[0m\e[93m unzips the db binary that was moved to selected server \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'db_install'\e[0m\e[93m installs the db binary that was moved to selected server \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'check_db_binary_install_status'\e[0m\e[93m enables check of status of db installation initiated by 'db_install' \e[0m"
		echo -ne "\n"	
		echo -e "\e[96m 'db_patch_binary_unpack'\e[0m\e[93m unzips the db patch binary that was moved to selected server \e[0m"
		echo -ne "\n"						
		echo -e "\e[96m 'db_patch_binary_install'\e[0m\e[93m installs the db patch binary that was moved to selected server \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'check_db_patch_binary_install_status'\e[0m\e[93m enables check of status of installation initiated by 'db_patch_binary_install' \e[0m"
		echo -ne "\n"		
		echo -e "\e[96m 'ebs_web_shutdown'\e[0m\e[93m shuts down ebs web \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'ebs_web_start'\e[0m\e[93m starts up ebs web \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'check_ebs_web_status'\e[0m\e[93m enables to check run status of ebs web \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'oacore_shutdown'\e[0m\e[93m shuts down oacore \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'oacore_start'\e[0m\e[93m starts up oacore \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'check_oacore_status'\e[0m\e[93m enables to check run status of oacore \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'concurrent_manager_shutdown'\e[0m\e[93m shuts down concurrent manager \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'concurrent_manager_abort'\e[0m\e[93m shuts down concurrent manager forcefully \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'concurrent_manager_start'\e[0m\e[93m starts up concurrent manager \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'check_concurrent_manager_status'\e[0m\e[93m enables to check run status of concurrent manager \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'tomcat_restart'\e[0m\e[93m shuts down and subsequently starts up tomcat \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'tomcat_shutdown'\e[0m\e[93m shuts down tomcat \e[0m"
		echo -ne "\n"	
		echo -e "\e[96m 'tomcat_start'\e[0m\e[93m starts up tomcat \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'check_tomcat_status'\e[0m\e[93m enables to check run status of tomcat \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'MD120_i673_install'\e[0m\e[93m performs MD120, the post installation steps for i673. Subsequently executes'check_MD120_i673_install_status', 'tomcat_shutdown', 'check_tomcat_status', 'tomcat_start', 'check_tomcat_status', 'MD120_i673_deployAOS', 'check_MD120_i673_deployAOS_status', 'MD120_i673_deployAAS', 'check_MD120_i673_deployAAS_status', 'MD120_i673_deployAIS', 'check_MD120_i673_deployAIS_status' \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'MD120_f694_f695_install', 'MD120_f694_f695_install_sub_process'\e[0m\e[93m performs MD120, the post installation steps for f694 and f695 \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'shared_pool_flush'\e[0m\e[93m performs shared pool flush \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'validate_objects'\e[0m\e[93m re-compiles invalid objects and lists number of invalid objects pre and post run. This is executed in connection to an binary installlation \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'validate_objects_standalone'\e[0m\e[93m re-compiles invalid objects and lists number of invalid objects pre and post run \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'JAR_signer'\e[0m\e[93m uses DB_build.java to create index file for application patch \e[0m"
		echo -ne "\n"	
		echo -e "\e[96m 'tukku_mock'\e[0m\e[93m sets up tukku mock on the selected environment \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'tukku_live'\e[0m\e[93m installs i482 files to enable tukku live on the selected environment \e[0m"
		echo -ne "\n"	
		echo -e "\e[96m 'middle_tier_restart'\e[0m\e[93m executes JAR signer, ebs_web_retart, oacore_restart \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'cm_restart'\e[0m\e[93m executes concurrent manager restart and shared pool flush for an instalaltion initiated \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'installation_summary'\e[0m\e[93m provides the summary of the installation completed \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'deploy_to_env'\e[0m\e[93m enables user to deploy the same active binary created to one another environment\e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'reset_installation_status'\e[0m\e[93m resets the installatino status and allows user to install the same binaires on another environment. This is used with target 'deploy_to_env' \e[0m"
		echo -ne "\n"
		echo -e "\e[96m 'check_invalid_objects'\e[0m\e[93m gets the count of invalid objects. This is used with target 'validate_objects' \e[0m"
		echo -ne "\n"
		</shellscript>		
	</target>
	
<!-- Mandatory path definition to be secured on remote server -->

	<!-- Ensure to create folder CEB_PACK & CEBDB_PACK on home path of application server -->
	<property name="app_patch_host_path" value="CEB_PACK"/>
	<property name="db_patch_host_path" value="CEBDB_PACK"/>	
	<!-- Ensure to alias for XXFI_TOP & XXFE_TOP on home path of application server -->
	<property name="xxfetop_host_path" value="XXFE_TOP"/>
	<property name="xxfitop_host_path" value="XXFI_TOP"/> 
	<property name="app_home" value="HOME"/> 

<!-- Mandatory path definition to be secured on remote server -->


<!-- Path definition for build workspace -->

	<property name="build" location="./build"/>
	<property name="db_build" location="./db_build"/>
	<property name="classes" location="./classes"/>
	<property name="src" location="./source"/>
	<property name="tmp" location="./tmp"/>
	<property name="Flags" location="./Flags"/>
	<property name="oracle" location="./oracle"/>
	<property name="jarfiles" location="jarfiles"/>	
	<property name="com" location="./com"/>		
	<property name="properties" location="./properties"/>
	<property name="tukku" location="tukku"/>
	<property name="validate_objects" location="./validate_objects"/>	
	<property name="transit_point" location="./transit_point"/>
	<property name="robot" location="./robot/ConfiguratorJARUpload/"/>
	<property name="jar_folder" location="./JAR/project"/>
	<property name="jar_apps_java" location="${jar_folder}/oracle/apps"/>
	<property name="jar_db_java" location="${jar_folder}/com/teliasonera/ebs"/>
	<property name="jar_java_class" location="${jarfiles}/oracle/apps"/>
	<property name="jar_db_class" location="${jarfiles}/com/teliasonera/ebs"/>
	<property name="jar_compiled_jar" location="./compiled_jar"/>
	<property name="utility_space" location="./utility"/>
	<property name="installation_logs" location="./logs"/>
	<property name="git_alpha2_checkout_root" location="./alpha2_ebs"/>
	<property name="xxfe_root" location="${git_alpha2_checkout_root}/xxfe/12.0.0/install/"/>
	<property name="xxfi_root" location="${git_alpha2_checkout_root}/xxfi/12.0.0/install/"/>
	<property name="xxfi_db_root" location="${git_alpha2_checkout_root}/xxfi_db/"/>
	<property name="extra_antlib" location="./extra_antlib"/>

<!-- Path definition for build workspace -->		


<!-- Path definition for build runtime files -->

	<property name="list_affected_features_file" location="${tmp}/affected_features_list.lst"/>
	<property name="branch_to_build_property_file" location="${tmp}/branch.properties"/>
	<property name="branch_version_property_file" location="${properties}/version.properties"/>
	<property name="active_deploy_env_property_file" location="${tmp}/active_deploy_env_property_file.properties"/>
	<!--property name="installation_to_include_property_file" location="${tmp}/installation_to_include.properties"/-->
	<property name="installation_status_property_file" location="${tmp}/installation_status.properties"/>
	<property name="app_build_checkout_file" location="${utility_space}/app_build_checkout.sh"/>
	<property name="db_build_checkout_file" location="${utility_space}/db_build_checkout.sh"/>
	<property name="jar_build_checkout_file" location="${utility_space}/jar_build_checkout.sh"/>
	<property name="db_install_build_checkout_file" location="${utility_space}/db_install_build_checkout.sh"/>		
	<property name="app_build_property_file" location="${tmp}/temp.properties"/>
	<property name="db_build_property_file" location="${tmp}/temp_db.properties"/>
	<property name="build_property_file" location="${properties}/build.properties"/>
	<property name="artifactory_file_status_property_file" location="${transit_point}/artifactory_file_status.properties"/>
	<property name="branches_to_delete_file" location="${utility_space}/branches_to_delete.txt"/>
	<property name="tags_to_delete_file" location="${utility_space}/tags_to_delete.txt"/>
    <property name="rc_branch_property_file" location="${utility_space}/rc_branch.properties"/> <!-- New -->
	<property name="tmp_rc_branch_property_file" location="${tmp}/rc.properties"/> <!-- New -->
	<property name="release_version_property_file" location="${properties}/rc-version.properties"/> <!-- New -->



<!-- Path definition for build runtime files-->


<!-- Path definition for build MD120_JSP_Installation_xxfe_f694_f695-->

	<property name="MD120_JSP_Installation_xxfe_f694_f695_file" location="${utility_space}/MD120_JSP_Installation_xxfe_f694_f695.lst"/>

<!-- Path definition for build MD120_JSP_Installation_xxfe_f694_f695-->


<!--Property load-->

	<loadproperties srcFile="${properties}/build.properties"/>
	<loadproperties srcFile="${properties}/scp.properties"/>
	<!--loadproperties srcFile="${db_build_property_file}"/>	
	<loadproperties srcFile="${app_build_property_file}"/-->

<!--Alternate way to load same properties-->
	<property file="${db_build_property_file}"/>	
	<property file="${app_build_property_file}"/>		

<!--Property load-->


<!-- Path definition for build scripts -->

	<property name="prepare_module_build_checkout_file" location="${utility_space}/prepare_module_build_checkout.sh"/>
	<property name="prepare_file_build_checkout_file" location="${utility_space}/prepare_file_build_checkout.sh"/>

<!-- Path definition for build scripts -->

<!-- Path definition for ${d2u} on jenkins fi000verso4 -->

	<property name="dos2unix-7.3.3" location="./dos2unix-7.3.3"/>
	<property name="d2u" location="/data/home/jenkins/workspace/a2-dev/A2_CM_INFRA/dos2unix-7.3.3/dos2unix-7.3.3/bin/dos2unix"/>			
<!-- Path definition for dos2unix on jenkins fi000verso4 -->

<!--**************************************** START OF BASELINED CORE TARGETS ****************************************************-->

<!-- Start of targets to set clock -->

	<target name="clock">
		<tstamp>
    		<format property="clock" timezone="CET" pattern="EEE, MMM dd, yyyy hh:mm:ss a z" locale="en,GB"/>
    		<!-- Choice timezone="CET" is identified to match the timezone followed by application/db server -->
		</tstamp>
		<echo message="${clock}"/>
	</target>

<!-- End of targets to set clock -->	


<!-- Start of targets to manage workspace -->

	<target name="setup_build_workspace" description="folder clean-up">
		<!-- Empty the ${build} and ${tmp} directories -->
		<echo message="package folders"/>
		<echo message=""/>
		<mkdir dir="${build}"/>
		<mkdir dir="${db_build}/lst"/>
		<mkdir dir="${jar_folder}"/>
		<mkdir dir="${jarfiles}"/>				
		<mkdir dir="${classes}"/>
		<mkdir dir="${Flags}"/>
		<mkdir dir="${tmp}"/>
		<mkdir dir="${installation_logs}"/>
		<mkdir dir="${tukku}"/>
		<mkdir dir="${validate_objects}"/>
		<mkdir dir="${transit_point}"/>

		<unzip src="${src}/lib.zip" dest="${jar_folder}"/>

		<shellscript shell="bash">
    		chmod -R 777 "${build}"
    		chmod -R 777 "${db_build}"
    		chmod -R 777 "${utility_space}"
    		chmod -R 777 "${properties}"
    		chmod -R 777 "${robot}"
    		chmod -R 777 "${src}"
    		chmod -R 777 "${jar_folder}"
    		chmod -R 777 "${extra_antlib}"
    		chmod -R 777 "${jarfiles}"
    		chmod -R 777 "${classes}"
    		chmod -R 777 "${Flags}"
    		chmod -R 777 "${tmp}"
    		chmod -R 777 "${installation_logs}"
    		chmod -R 777 "${tukku}"
    		chmod -R 777 "${validate_objects}"
		</shellscript>
		<antcall target="compile"/>
	</target>	

<!-- compile target for https://versojenkins.eficode.com/ -->

		<!--exec executable="/bin/bash">
  			<env key="JAVA_HOME" value="/opt/tools/jdk1.8.0_181"/>
  			<env key="PATH" path="/opt/tools/jdk1.8.0_181/bin"/>
		</exec-->	

	<target name="compile">

    	<property name="debuglevel" value="source,lines,vars"/>
    	<property name="target" value="1.8"/>
    	<property name="source" value="1.8"/>
    	<property name="compiler" value="modern"/>  <!-- <property name="compiler" value="javac1.8"/> -->
    	<property name="executable" value="/opt/tools/jdk1.8.0_181/bin/javac"/>		

		<!-- setting_java_home for https://versojenkins.eficode.com/ -->
		<shellscript shell="bash">
    		export JAVA_HOME=
    		export PATH=
    		export JAVA_HOME="/opt/tools/jdk1.8.0_181"
    		export PATH="/opt/tools/jdk1.8.0_181/bin"    		
    		echo $JAVA_HOME
    		echo $PATH    		
		</shellscript>			

		<echo message="================================================== Setup for Alpha2_Build-Deploy_Infra ============================================"/>
		<javac fork="yes" includeantruntime="true" debug="on" debuglevel="${debuglevel}" compiler="${compiler}" srcdir="${src}" destdir="${classes}" source="${source}" target="${target}"/>
		<echo message="================================================== Setup for Alpha2_Build-Deploy_Infra ============================================"/>
	</target>	
<!-- compile target for https://versojenkins.eficode.com/ -->

	<target name="clean" description="folder clean-up">
		<!-- Empty the ${build} and ${tmp} directories -->
		<echo message="package folders"/>
		<echo message=""/>
		<delete dir="${build}"/>
		<delete dir="${db_build}"/>
		<delete dir="${tmp}"/>
		<delete dir="${Flags}"/>
		<delete dir="${validate_objects}"/>
		<delete dir="${tukku}"/>
		<delete dir="${transit_point}"/>
		<echo message=""/>
		<echo message="jar folders"/>
		<echo message=""/>
		<delete dir="${jar_java_class}"/>
		<delete dir="${jar_db_class}"/>
		<delete dir="${jar_apps_java}"/>
		<delete dir="${jar_db_java}"/>		
		<delete dir="${jar_compiled_jar}"/>	
	</target>

	<target name="init" depends="clean">
		<mkdir dir="${build}"/>
		<mkdir dir="${db_build}/lst"/>
		<mkdir dir="${tmp}"/>
		<mkdir dir="${Flags}"/>
		<mkdir dir="${jar_folder}"/>
		<mkdir dir="${jarfiles}"/>
		<mkdir dir="${validate_objects}"/>
		<mkdir dir="${tukku}"/>
		<mkdir dir="${transit_point}"/>
	</target>

	<target name="clean_build_workspace" description="folder clean-up">
		<!-- Empty the ${build} and ${tmp} directories -->
		<echo message="package folders"/>
		<echo message=""/>
		<delete dir="${build}"/>
		<delete dir="${db_build}"/>		
		<delete dir="${Flags}"/>
		<delete dir="${validate_objects}"/>
		<delete dir="${tukku}"/>
		<delete dir="${transit_point}"/>
		<echo message=""/>
		<echo message="jar folders"/>
		<echo message=""/>
		<delete dir="${jar_java_class}"/>
		<delete dir="${jar_db_class}"/>
		<delete dir="${jar_apps_java}"/>
		<delete dir="${jar_db_java}"/>
		<delete dir="${jar_compiled_jar}"/>	
	</target>

	<target name="init_build_workspace" depends="clean_build_workspace">
	<!-- Target specific to create_rollback_binaries. Similar to 'init', but does not initialize 'tmp' folder -->			
		<mkdir dir="${build}"/>
		<mkdir dir="${db_build}/lst"/>
		<mkdir dir="${Flags}"/>
		<mkdir dir="${jar_folder}"/>
		<mkdir dir="${jarfiles}"/>
		<mkdir dir="${validate_objects}"/>
		<mkdir dir="${tukku}"/>
		<mkdir dir="${transit_point}"/>			
	</target>

<!-- End of targets to manage workspace -->	


<!-- Start of git targets -->

	<target name="git_clone" depends="clock">
		<echo message="==================================================================================================================================="/>
		<echo message="============================================================ git_clone ============================================================"/>
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>		
		<echo message=""/>			
		<shellscript shell="bash">
			    git clone "https://""${git.username}"":""${git.password}""@""${git.host}"
		</shellscript>
		<echo message=""/>		
		<echo message="B_End: ${clock}"/>		
		<echo message=""/>
		<echo message="============================================================ git_clone ============================================================"/>
		<echo message="==================================================================================================================================="/>		
	</target>	

	<target name="branch_checkout">
		<echo message="==================================================================================================================================="/>
		<echo message="======================================================= git_branch_checkout ======================================================="/>		
		<shellscript shell="bash">
				echo ""
			    git_branch=`grep 'git_branch' "${branch_to_build_property_file}" | cut -d '=' -f 2`
			    cd "${git_alpha2_checkout_root}"
			    git checkout "${git_branch}"
			    echo ""
				git pull --rebase			    
			    echo ""			    
		</shellscript>
		<echo message="======================================================= git_branch_checkout ======================================================="/>			
		<echo message="==================================================================================================================================="/>			
	</target>

	<target name="branch_checkout_master">
		<echo message="==================================================================================================================================="/>
		<echo message="=================================================== git_branch_checkout_master ===================================================="/>		
		<shellscript shell="bash">
			    cd "${git_alpha2_checkout_root}"
			    git checkout master
			    echo ""
				git pull --rebase			    
			    echo ""			    
		</shellscript>
		<echo message="=================================================== git_branch_checkout_master ===================================================="/>			
		<echo message="==================================================================================================================================="/>			
	</target>

	<target name="delete_branches">
		<echo message="==================================================================================================================================="/>
		<echo message="========================================================== delete_branches ========================================================"/>
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>		
		<echo message=""/>

		<fail message="Oooops !!! branches_to_delete.txt is missing. Please check the input">
    		<condition>
        		<not>
            		<resourcecount count="1">
                		<fileset id="fs" dir="${utility_space}" includes="branches_to_delete.txt"/>
            		</resourcecount>
        		</not>
    		</condition>
		</fail>		

		<shellscript shell="bash">
				echo ""
				cd "${git_alpha2_checkout_root}"
				git checkout master
				git pull rebase			    
			    echo ""			    
		</shellscript>			

		<loadfile property="file" srcfile="${branches_to_delete_file}"/>
		<foreach list="${file}" target="delete_branch_sub_process" param="branch_name" delimiter="${line.separator}" inheritall="true"/>
		<echo message=""/>			
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="========================================================== delete_branches ========================================================"/>		
		<echo message="==================================================================================================================================="/>
	</target>	

	<target name="delete_branch_sub_process" depends="clock">
		<shellscript shell="bash">
				echo ""
			    cd "${git_alpha2_checkout_root}"
			    git push origin --delete  "${branch_name}"
			    echo ""
		</shellscript>
	</target>

	<target name="delete_tags">
		<echo message="==================================================================================================================================="/>
		<echo message="============================================================ delete_tags =========================================================="/>
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>		
		<echo message=""/>

		<fail message="Oooops !!! tags_to_delete.txt is missing. Please check the input">
    		<condition>
        		<not>
            		<resourcecount count="1">
                		<fileset id="fs" dir="${utility_space}" includes="tags_to_delete.txt"/>
            		</resourcecount>
        		</not>
    		</condition>
		</fail>		

		<shellscript shell="bash">
				echo ""
				cd "${git_alpha2_checkout_root}"
				git checkout master
				git pull rebase			    
			    echo ""			    
		</shellscript>			

		<loadfile property="file" srcfile="${tags_to_delete_file}"/>
		<foreach list="${file}" target="delete_tag_sub_process" param="tag_name" delimiter="${line.separator}" inheritall="true"/>
		<echo message=""/>			
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="============================================================ delete_tags =========================================================="/>		
		<echo message="==================================================================================================================================="/>
	</target>	

	<target name="delete_tag_sub_process" depends="clock">
		<shellscript shell="bash">
				echo ""
			    cd "${git_alpha2_checkout_root}"
			    git push origin --delete  "${tag_name}"
			    echo ""
		</shellscript>
	</target>			


	<!--Refernce: To fetch date with timestamp "d=`date +%Y%m%d-%H%M`"-->

	<target name="branch_autotag">
		<echo message="==================================================================================================================================="/>
		<echo message="========================================================= branch_autotag =========================================================="/>	

		<property name="branch_name" value="not_set_from_command_line"/>

		<antcall target="build_branch"/>
		<antcall target="branch_checkout_master"/>
		<antcall target="branch_checkout"/>		

		<shellscript shell="bash">

				git_branch=`grep 'git_branch' "${branch_to_build_property_file}" | cut -d '=' -f 2`

			    d=`date +%Y%m%d`

			    tag_name=`echo ${git_branch}_"autotag"-$d`
			    echo $tag_name	

			    cd "${git_alpha2_checkout_root}"

			    git tag -a $tag_name -m $tag_name
			    git push origin $tag_name				    

		</shellscript>

		<antcall target="branch_checkout_master"/>		

		<echo message="========================================================= branch_autotag =========================================================="/>		
		<echo message="==================================================================================================================================="/>			
	</target>	


	<target name="list_affected_features">
		<echo message="==================================================================================================================================="/>
		<echo message="====================================================== list_affected_features ====================================================="/>

		<antcall target="branch_checkout_master"/>
		<antcall target="branch_checkout"/>

		<shellscript shell="bash">
			    git_branch=`grep 'git_branch' "${branch_to_build_property_file}" | cut -d '=' -f 2`
				cd "${git_alpha2_checkout_root}"
			    rm -f "${list_affected_features_file}"
			    git diff --name-only master.."${git_branch}" >> "${list_affected_features_file}"

			    echo ""			    
			    echo "=============================== files included for build ==========================="
			    echo ""			    		    			    
			    cat "${list_affected_features_file}"
			    echo ""			    
			    echo "=============================== files included for build ==========================="			    
			    echo ""			    
		</shellscript>

		<antcall target="branch_checkout_master"/>		

		<echo message="====================================================== list_affected_features ====================================================="/>				
		<echo message="==================================================================================================================================="/>
	</target>

	<target name="list_affected_features_between_tags">
		<echo message="==================================================================================================================================="/>
		<echo message="=============================================== list_affected_features_between_tags ==============================================="/>

		<antcall target="branch_checkout_master"/>				
		<antcall target="branch_checkout"/>
		<shellscript shell="bash">
			    git_branch=`grep 'git_branch' "${branch_to_build_property_file}" | cut -d '=' -f 2`
			    build_tag1=`grep 'build_tag1' "${branch_to_build_property_file}" | cut -d '=' -f 2`
			    build_tag2=`grep 'build_tag2' "${branch_to_build_property_file}" | cut -d '=' -f 2`
				cd "${git_alpha2_checkout_root}"
			    rm -f "${list_affected_features_file}"
			    git diff --name-only "${build_tag1}".."${build_tag2}" >> "${list_affected_features_file}"

			    echo ""			    
			    echo "=============================== files included for build ==========================="		    			    
			    echo ""			    
			    cat "${list_affected_features_file}"
			    echo ""			    
			    echo "=============================== files included for build ==========================="			    
			    echo ""			    
		</shellscript>
		<antcall target="branch_checkout_master"/>		
		<echo message="=============================================== list_affected_features_between_tags ==============================================="/>
		<echo message="==================================================================================================================================="/>
	</target>

	<target name="list_affected_features_between_autotags">
		<echo message="==================================================================================================================================="/>
		<echo message="=============================================== list_affected_features_between_autotag ============================================"/>

		<antcall target="branch_checkout_master"/>
		<antcall target="branch_checkout"/>

		<shellscript shell="bash">

			    rm -f "${list_affected_features_file}"				

				git_branch=`grep 'git_branch' "${branch_to_build_property_file}" | cut -d '=' -f 2`

				d=`date +%Y%m%d`
				sd=`date --date="yesterday" +%Y%m%d`
				tag_name_d=`echo ${git_branch}_"autotag"-$d`
				tag_name_sd=`echo ${git_branch}_"autotag"-$sd`

			    echo "=============================== tags included for build ==========================="		    			    
			    echo ""				
				echo "tag1 (system_date): "$tag_name_d
				echo "tag1 (system_date-1): "$tag_name_sd
			    echo ""
			    echo "=============================== tags included for build ==========================="	

				cd "${git_alpha2_checkout_root}"

			    git diff --name-only $tag_name_d..$tag_name_sd >> "${list_affected_features_file}"

			    echo ""
			    echo "=============================== files included for build ==========================="		    			    
			    echo ""
			    cat "${list_affected_features_file}"
			    echo ""
			    echo "=============================== files included for build ==========================="					    
			    echo ""			    
		</shellscript>

		<antcall target="branch_checkout_master"/>

		<echo message="=============================================== list_affected_features_between_autotag ============================================"/>
		<echo message="==================================================================================================================================="/>
	</target>

	<!--

				cd "${git_alpha2_checkout_root}"

			    git diff name-only $tag_name_d..$tag_name_sd
			    git diff name-only $tag_name_d..$tag_name_sd >> "${list_affected_features_file}"			    
			    echo "=============================== files included for build ==========================="		    			    
			    cat "${list_affected_features_file}"
			    echo "=============================== files included for build ==========================="	

	-->


<!-- Start of utility target -->

	<!--target name="check_status_old" depends="clock">
		<echo message="==================================================================================================================================="/>
		<echo message="=============================================== ${module_to_check_status} ========================================================="/>
		<echo message=""/>			
		<echo message="B_Start: ${clock}"/>		
		<echo message=""/>	

		<shellscript shell="bash">
		 	temp_properties=tmp/temp.properties
		 	echo "$temp_properties" 
		 	#created during setup-test/prod-package
		 	package_name=`grep package.name $temp_properties | cut -d '=' -f 2`
		 	echo "$package_name"
		 	branch_name=`grep 'git_branch' "${branch_to_build_property_file}" | cut -d '=' -f 2`
		 	echo "$branch_name"
			cd "${utility_space}"
			sh check_status_old.sh "${module_to_check_status}" "${status_to_check}" "${time_frequency_to_check_status}" "${count_frequency_to_check_status}" "$branch_name" "$package_name" "${property_to_set}"	
		</shellscript>	

		<echo message=""/>				
		<echo message="B_End: ${clock}"/>
		<echo message=""/>			
		<echo message="=============================================== ${module_to_check_status} ========================================================="/>
		<echo message="==================================================================================================================================="/>
	</target-->
	
	<target name="check_status" depends="clock">
		<echo message="==================================================================================================================================="/>
		<echo message="===================================================== ${module_to_check_status} ==============================================================="/>
		<echo message=""/>			
		<echo message="B_Start: ${clock}"/>		
		<echo message=""/>	

		<shellscript shell="bash">
			sh "${utility_space}"/check_status.sh "${module_to_check_status}" "${status_to_check}" "${time_frequency_to_check_status}" "${count_frequency_to_check_status}" "${brch_name}" "${pkg_name}" "${hst_name}" "${property_to_set}"	
		</shellscript>	

		<echo message=""/>				
		<echo message="B_End: ${clock}"/>
		<echo message=""/>			
		<echo message="===================================================== ${module_to_check_status} ==============================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>	

	<target name="check_status_trial" depends="clock">

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>

		<antcall target="check_status">
			<!--param name="module_to_check_status" value="concurrent_manager_status"/>
			<param name="status_to_check" value="Not Active at this time"/>
			<param name="time_frequency_to_check_status" value="5s"/>
			<param name="count_frequency_to_check_status" value="2"/>			
			<param name="property_to_set" value="concurrent_manager_shutdown"/>

			<param name="module_to_check_status" value="app_binary_install_status"/>
			<param name="status_to_check" value="Compiling finished"/>
			<param name="time_frequency_to_check_status" value="1m"/>
			<param name="count_frequency_to_check_status" value="1"/>
			<param name="brch_name" value="${branch_name}"/>							
			<param name="pkg_name" value="${package_name}"/>		
			<param name="hst_name" value="${host_name}"/>							
			<param name="property_to_set" value="app_binary_install_complete"/>

			<param name="module_to_check_status" value="tomcat_status"/>
			<param name="status_to_check" value="UP"/>
			<param name="time_frequency_to_check_status" value="1m"/>
			<param name="count_frequency_to_check_status" value="1"/>
			<param name="brch_name" value="${branch_name}"/>							
			<param name="pkg_name" value="${package_name}"/>		
			<param name="hst_name" value="${host_name}"/>							
			<param name="property_to_set" value="tomcat_up"/-->

			<param name="module_to_check_status" value="oacore_status"/>
			<param name="status_to_check" value="running."/>
			<param name="time_frequency_to_check_status" value="30s"/>
			<param name="count_frequency_to_check_status" value="2"/>
			<param name="brch_name" value="${branch_name}"/>							
			<param name="pkg_name" value="${package_name}"/>		
			<param name="hst_name" value="${host_name}"/>							
			<param name="property_to_set" value="oacore_start"/>				

		</antcall>	
	</target>


	<target name="check_invalid_objects" depends="clock">

		<echo message="==================================================================================================================================="/>
		<echo message="=================================================== ${installation_state} ==========================================================="/>

		<shellscript shell="bash">

			package_to_check=`cat ./tmp/temp.properties | grep package.name | cut -d '=' -f 2`
			branch_to_check=`cat ./tmp/branch.properties | grep git_branch | cut -d '=' -f 2`
			host_to_check=`cat ./tmp/active_deploy_env_property_file.properties | grep hostname | cut -d '=' -f 2`

			echo ${installation_state} >> tmp/count_of_invalid_objects.log
			echo "-------------------------------------------------------------" &amp;>> tmp/count_of_invalid_objects.log

			grep -A3 "SQL>" logs/"${branch_to_check}"/"${package_to_check}"/"${host_to_check}"/validate_objects.log | grep -A2 "COUNT" | head -3 | sed -e 's/COUNT(\*)/COUNT OF INVALID OBJECTS BEFORE EXECUTING VALIDATE OBJECTS/' &amp;>> tmp/count_of_invalid_objects.log


			grep -A3 "SQL>" logs/"${branch_to_check}"/"${package_to_check}"/"${host_to_check}"/validate_objects.log | grep -A2 "COUNT" | tail -3 | sed -e 's/COUNT(\*)/COUNT OF INVALID OBJECTS AFTER EXECUTING VALIDATE OBJECTS/' &amp;>> tmp/count_of_invalid_objects.log

			echo "-------------------------------------------------------------" &amp;>> tmp/count_of_invalid_objects.log

			echo "" &amp;>> tmp/count_of_invalid_objects.log


			cat tmp/count_of_invalid_objects.log

        </shellscript>

		<echo message="=================================================== ${installation_state} ==========================================================="/>
		<echo message="==================================================================================================================================="/>

	</target>


	<target name="reset_installation_status" depends="clock">
	<!-- Target enables to reset the completed installation status and deploy the same binaries to another environment -->

		<echo message="==================================================================================================================================="/>
		<echo message="================================================ reset_installation_status ========================================================"/>		
		
		<shellscript shell="bash">

			package_to_check=`cat ./tmp/temp.properties | grep package.name | cut -d '=' -f 2`
			branch_to_check=`cat ./tmp/branch.properties | grep git_branch | cut -d '=' -f 2`
			host_to_check=`cat ./tmp/active_deploy_env_property_file.properties | grep hostname | cut -d '=' -f 2`

			cp tmp/installation_status.properties logs/"${branch_to_check}"/"${package_to_check}"/"${host_to_check}"/tmp/

			rm tmp/installation_status.properties

			cp tmp/installation_status_bkup.properties tmp/installation_status.properties

        </shellscript>

		<echo message="================================================ reset_installation_status ========================================================"/>
		<echo message="==================================================================================================================================="/>

	</target>


	<target name="check_invalid_objects_trial" depends="clock">

		<antcall target="check_invalid_objects">
			<param name="installation_state" value="AFTER_ENV_RESTARTS"/>
		</antcall>
	</target>


	<target name="installation_summary" depends="clock">
		<shellscript shell="bash">

			cat tmp/installation_status.properties

        </shellscript>
	</target>	


	<target name="copyfiles">
		<echo message=""/>		

    	<copy includeemptydirs="false" todir="${tmp}">
            <fileset dir="${build}"/>
    	</copy> 

		<echo message=""/>
	</target>


	<target name="load_binaries_from_artifactory">
		<echo message="==================================================================================================================================="/>
		<echo message="=============================================== load_binaries_from_artifactory ===================================================="/>
		<echo message=""/>

		<loadproperties srcFile="${build_property_file}"/>
		<propertycopy name="artifactory_host" from="artifactory.test.host"/>
		<propertycopy name="artifactory_uid" from="artifactory.username"/>
		<propertycopy name="artifactory_pwd" from="artifactory.password"/>	

		<property name="package_name" value="not_set_from_command_line"/>

		<if>
			<equals arg1="${package_name}" arg2="not_set_from_command_line" casesensitive="No"/>
				<then>
					<var name="package_name" unset="true"/>
					<input message="Please enter the binary name?" addProperty="package_name"/>
				</then>
		</if>

		<if>
			<not>
				<equals arg1="${package_name}" arg2="not_set_from_command_line" casesensitive="No"/>
			</not>

				<then>
					<echo message="binary_file_to_search: ${package_name}.tar.gz"/>

					<antcall target="checkfile_at_artificatory">			
						<param name="binary_file" value="${package_name}.tar.gz"/>
						<param name="destination" value="${artifactory_host}"/>
						<param name="uid" value="${artifactory_uid}"/>
						<param name="pwd" value="${artifactory_pwd}"/>						
					</antcall>

					<loadfile property="file" srcfile="${transit_point}/checkfile_at_artifactory_output.log"/>
					<propertyregex property="status_code" input="${file}" regexp="(HTTP\/1.1.)(\d+)" select="\2" override="true"/>
					<echo message="status_code: ${status_code}"/>

					<if>
						<equals arg1="${status_code}" arg2="200"/>
							<then>
								<echo message=""/>
								<shellscript shell="bash">
									touch "${artifactory_file_status_property_file}"
									./utility/setprop.sh "file_available_at_artifactory" "0" "${artifactory_file_status_property_file}"
								</shellscript>

								<antcall target="download_from_artifactory">			
									<param name="binary_file" value="${package_name}.tar.gz"/>
									<param name="artifactory_host" value="${artifactory_host}"/>
									<param name="uid" value="${artifactory_uid}"/>
									<param name="pwd" value="${artifactory_pwd}"/>											
									<param name="download_path" value="${transit_point}"/>
								</antcall>

								<antcall target="untar">	<!-- Extract files to transit_point folder -->
									<param name="file_location" value="${transit_point}"/>
									<param name="file_to_extract" value="${package_name}.tar.gz"/>			
									<param name="file_extract_destination" value="${transit_point}"/>
								</antcall>

								<loadproperties  srcFile="${transit_point}/tmp/branch.properties"/> <!-- Extract branch_name -->
								<propertycopy name="branch_name" from="git_branch"/>
								<mkdir dir="${installation_logs}/${branch_name}/${package_name}"/> <!-- Create desired location -->

								<antcall target="untar"> <!-- Extract files to desired location -->			
									<param name="file_location" value="${transit_point}"/>
									<param name="file_to_extract" value="${package_name}.tar.gz"/>			
									<param name="file_extract_destination" value="${installation_logs}/${branch_name}/${package_name}"/>
								</antcall>

								<!--move file="${package_name}.tar.gz" todir="${transit_point}"/-->

								<move todir="${tmp}"> <!-- Get tmp folder ready for installation -->	
  									<fileset dir="${transit_point}/tmp">
	    								<include name="**/*"/>
  									</fileset>
								</move>

								<!--delete includeemptydirs="true"--> <!-- Remove transit files -->
  									<!--fileset dir="${transit_point}" includes="**/*"/-->
  									<!--fileset dir="${transit_point}/binaries" includes="**/*"/>
  									<fileset dir="${transit_point}/tmp" includes="**/*"/>
  									<fileset dir="${transit_point}" includes="${package_name}.tar.gz"/-->
								<!--/delete-->						

							</then>
							<else>
								<echo message="===================================== ! Alert ! =========================================="/>			
								<echo message="============ Error reading file availability. Require manual intervention ================"/>
								<echo message="===================================== ! Alert ! =========================================="/>	
								<shellscript shell="bash">
									touch "${artifactory_file_status_property_file}"
									./utility/setprop.sh "file_available_at_artifactory" "E" "${artifactory_file_status_property_file}"
								</shellscript>										
							</else>
					</if>					
			<echo message=""/>
			</then>
		</if>
		<echo message=""/>					
		<echo message="=============================================== load_binaries_from_artifactory ===================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>	

	<target name="test_tar">
		<echo message=""/>	

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>			

		<antcall target="tar">			
			<param name="dir_to_compress" value="${installation_logs}/${branch_name}/${package_name}"/>			
			<param name="destination" value="${installation_logs}/${branch_name}"/>
		</antcall>	

		<echo message=""/>
	</target>	

	<target name="tar">
		<echo message="==================================================================================================================================="/>
		<echo message="=============================================================== tar ==============================================================="/>

		<tar longfile="gnu" destfile="${destination}/${package_name}.tar" basedir="${dir_to_compress}"/>
		<gzip destfile="${destination}/${package_name}.tar.gz" src="${destination}/${package_name}.tar"/>
		<delete file="${destination}/${package_name}.tar"/>
	
		<echo message="=============================================================== tar ==============================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>

	<target name="test_zip">
		<echo message=""/>	

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>					

		<antcall target="zip">			
			<param name="dir_to_compress" value="${installation_logs}/${branch_name}/${package_name}"/>			
			<param name="destination" value="${installation_logs}/${branch_name}"/>
		</antcall>	

		<echo message=""/>
	</target>	

	<target name="zip">
		<echo message="==================================================================================================================================="/>
		<echo message="=============================================================== tar ==============================================================="/>

		<zip destfile="${destination}/${package_name}-logs.zip"
     		basedir="${dir_to_compress}"
     		includes="**/*.log,**/*.lst"
     		excludes="binaries/**,${host_name}/tmp/**,${package_name}*/${host_name}/tmp/**,${package_name}*/binaries/**"/>
	
		<echo message="=============================================================== tar ==============================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>	

	<target name="test_untar">
		<echo message=""/>	

		<antcall target="untar">			
			<param name="file_location" value="${transit_point}"/>
			<param name="file_to_extract" value="*.tar.gz"/>			
			<param name="file_extract_destination" value="${transit_point}"/>
		</antcall>	

		<echo message=""/>
	</target>		

	<target name="untar">
		<echo message="==================================================================================================================================="/>
		<echo message="============================================================== untar =============================================================="/>
		
		<basename property="file_name" file="${file_to_extract}" suffix=".tar.gz"/>

		<echo message="${file_location}"/>
		<gunzip src="${file_location}/${file_name}.tar.gz"/>
		<untar src="${file_location}/${file_name}.tar" dest="${file_extract_destination}"/>
		<delete file="${file_location}/${file_name}.tar"/>
	
		<echo message="============================================================== untar =============================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>

	<target name="test_upload_to_artifactory">
		<echo message=""/>	

		<loadproperties srcFile="${build_property_file}"/>
		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="build_target" from="build_target"/>


		<if>
			<equals arg1="${build_target_temp_var}" arg2="p" casesensitive="No"/>
				<then>
					<propertycopy name="artifactory_host" from="artifactory.prod.host"/>
					<propertycopy name="artifactory_uid" from="artifactory.username"/>
					<propertycopy name="artifactory_pwd" from="artifactory.password"/>
				</then>
			<else>
				<propertycopy name="artifactory_host" from="artifactory.test.host"/>
				<propertycopy name="artifactory_uid" from="artifactory.username"/>
				<propertycopy name="artifactory_pwd" from="artifactory.password"/>				
			</else>
		</if>

		<antcall target="upload_to_artifactory">			
			<param name="binary_file" value="${installation_logs}/${branch_name}/${package_name}.tar.gz"/>
			<param name="destination" value="${artifactory_host}"/>
			<param name="uid" value="${artifactory_uid}"/>
			<param name="pwd" value="${artifactory_pwd}"/>						
		</antcall>

		<antcall target="checkfile_at_artificatory">			
			<param name="binary_file" value="${package_name}.tar.gz"/>
			<param name="destination" value="${artifactory_host}"/>
			<param name="uid" value="${artifactory_uid}"/>
			<param name="pwd" value="${artifactory_pwd}"/>						
		</antcall>

		<loadfile property="file" srcfile="${transit_point}/checkfile_at_artifactory_output.log"/>
		<propertyregex property="status_code" input="${file}" regexp="(HTTP\/1.1.)(\d+)" select="\2" override="true"/>
		<echo message="status_code: ${status_code}"/>

		<if>
			<equals arg1="${status_code}" arg2="200"/>
				<then>
					<echo message=""/>
					<shellscript shell="bash">
						touch "${artifactory_file_status_property_file}"
						./utility/setprop.sh "file_available_at_artifactory" "0" "${artifactory_file_status_property_file}"
					</shellscript>						
				</then>
			<else>
				<echo message="===================================== ! Alert ! =========================================="/>			
				<echo message="============ Error reading file availability. Require manual intervention ================"/>
				<echo message="===================================== ! Alert ! =========================================="/>	
				<shellscript shell="bash">
					touch "${artifactory_file_status_property_file}"
					./utility/setprop.sh "file_available_at_artifactory" "E" "${artifactory_file_status_property_file}"
				</shellscript>										
			</else>
		</if>							

		<echo message=""/>
	</target>		

	<target name="upload_to_artifactory">
		<echo message="==================================================================================================================================="/>
		<echo message="================================================= upload_to_artifactory ==========================================================="/>

		<shellscript shell="bash">
    		curl -u "${uid}":"${pwd}" -T "${binary_file}" "${destination}"
		</shellscript>

		<echo message="================================================= upload_to_artifactory ==========================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>

	<target name="download_from_artifactory">
		<echo message="==================================================================================================================================="/>
		<echo message="=============================================== download_from_artifactory ========================================================="/>


		<shellscript shell="bash">
    		cd "${download_path}";
    		pwd;
    		echo "${binary_file}";     		
    		echo "${artifactory_host}";
    		curl -u "${uid}":"${pwd}" -O "${artifactory_host}"/"${binary_file}";
		</shellscript>


		<echo message="=============================================== download_from_artifactory ========================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>	


	<target name="checkfile_at_artificatory">
		<echo message="==================================================================================================================================="/>
		<echo message="================================================ checkfile_at_artificatory ========================================================"/>

		<loadproperties srcFile="${build_property_file}"/>	

		<shellscript shell="bash">
			curl -u "${uid}":"${pwd}" -I "${artifactory_host}"/"${binary_file}" > "${transit_point}/checkfile_at_artifactory_output.log"
			sleep 10s
    		cat "${transit_point}/checkfile_at_artifactory_output.log" | head -1
		</shellscript>
	
		<echo message="================================================ checkfile_at_artificatory ========================================================"/>
		<echo message="==================================================================================================================================="/>
	</target>

	<target name="download_versionproperty_from_artifactory">
		<echo message="==================================================================================================================================="/>
		<echo message="========================================= download_versionproperty_from_artifactory ==============================================="/>		

		<loadproperties srcFile="${build_property_file}"/>

		<propertycopy name="artifactory_host" from="artifactory.version.host"/>
		<propertycopy name="artifactory_uid" from="artifactory.username"/>
		<propertycopy name="artifactory_pwd" from="artifactory.password"/>		


		<antcall target="download_from_artifactory">			
			<param name="binary_file" value="version.properties"/>
			<param name="artifactory_host" value="${artifactory.version.host}"/>
			<param name="uid" value="${artifactory_uid}"/>
			<param name="pwd" value="${artifactory_pwd}"/>											
			<param name="download_path" value="${properties}"/>
		</antcall>

		<echo message="========================================= download_versionproperty_from_artifactory ==============================================="/>
		<echo message="==================================================================================================================================="/>
	</target>					

	<target name="upload_versionproperty_to_artifactory">
		<echo message="==================================================================================================================================="/>
		<echo message="=========================================== upload_versionproperty_to_artifactory ================================================="/>	

		<loadproperties srcFile="${build_property_file}"/>

		<propertycopy name="artifactory_host" from="artifactory.version.host"/>
		<propertycopy name="artifactory_uid" from="artifactory.username"/>
		<propertycopy name="artifactory_pwd" from="artifactory.password"/>


		<antcall target="upload_to_artifactory">			
			<param name="binary_file" value="${properties}/version.properties"/>
			<param name="destination" value="${artifactory.version.host}"/>
			<param name="uid" value="${artifactory_uid}"/>
			<param name="pwd" value="${artifactory_pwd}"/>						
		</antcall>

		<antcall target="checkfile_at_artificatory">			
			<param name="binary_file" value="version.properties"/>
			<param name="destination" value="${artifactory.version.host}"/>
			<param name="uid" value="${artifactory_uid}"/>
			<param name="pwd" value="${artifactory_pwd}"/>						
		</antcall>

		<loadfile property="file" srcfile="${transit_point}/checkfile_at_artifactory_output.log"/>
		<propertyregex property="status_code" input="${file}" regexp="(HTTP\/1.1.)(\d+)" select="\2" override="true"/>
		<echo message="status_code: ${status_code}"/>

		<if>
			<equals arg1="${status_code}" arg2="200"/>
				<then>
					<echo message=""/>
					<shellscript shell="bash">
						touch "${artifactory_file_status_property_file}"
						./utility/setprop.sh "file_available_at_artifactory" "0" "${artifactory_file_status_property_file}"
					</shellscript>						
				</then>
			<else>
				<echo message="===================================== ! Alert ! =========================================="/>			
				<echo message="============ Error reading file availability. Require manual intervention ================"/>
				<echo message="===================================== ! Alert ! =========================================="/>	
				<shellscript shell="bash">
					touch "${artifactory_file_status_property_file}"
					./utility/setprop.sh "file_available_at_artifactory" "E" "${artifactory_file_status_property_file}"
				</shellscript>										
			</else>
		</if>							

		<echo message="=========================================== upload_versionproperty_to_artifactory ================================================="/>	
		<echo message="==================================================================================================================================="/>
	</target>

	<target name="send_logs_by_email">
		<echo message=""/>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>

		<antcall target="send_email_with_attachment">			
			<param name="subject" value="a2-autodeploy-logs for review ${package_name}-logs.zip"/>
			<param name="replyto" value="alex.z.pandian@teliacompany.com"/>
			<param name="from" value="noreply-a2-autodeploy@teliacompany.com"/>
			<param name="tolist" value="alex.z.pandian@teliacompany.com"/>
			<param name="message" value="a2-autodeploy-logs for review"/>
			<param name="file_location" value="${installation_logs}/${branch_name}"/>
			<param name="file" value="${package_name}-logs.zip"/>
		</antcall>	

		<echo message=""/>
	</target>

	<target name="send_email_with_attachment">
		<loadproperties srcFile="${build_property_file}"/>

		<propertycopy name="smtp_host" from="smtp.host"/>
		<propertycopy name="smtp_port" from="smtp.port"/>	

		<mail mailhost="${smtp_host}" mailport="${smtp_port}" subject="${subject}" tolist="${tolist}">
  			<from address="${from}"/>
  			<replyto address="${replyto}"/>
  			<!--to address="harshath.balasubramanian@teliacompany.com"/-->
  			<message>${message}</message>
			<attachments>
				<fileset dir="${file_location}">
      				<include name="${file}"/>
    			</fileset>
  			</attachments>
		</mail>	
	</target>


<!-- End of utility target -->


<!-- Start of targets to move files -->

	<!-- Start of targets to move app binary -->

	<target name="scp_app_binary" depends="clock">	
		<echo message="==================================================================================================================================="/>		
		<echo message="======================================================== scp_app_binary ==========================================================="/>		
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>		
		<echo message=""/>		

		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>

		<antcall target="scp_to_remote">
			<!--param name="sourcedir" value="${build}"/-->
			<param name="sourcedir" value="${installation_logs}/${branch_name}/${package_name}/binaries/build"/>
			<param name="sourcefile" value="*.tar.gz"/>
			<param name="hostdir" value="${app_patch_host_path}"/>
			<param name="rusr" value="${apuser}"/>
			<param name="rpwd" value="${appasswd}"/>
			<param name="rhost" value="${aphost}"/>				
		</antcall>
		<echo message=""/>		
		<echo message="B_End: ${clock}"/>		
		<echo message=""/>
		<echo message="======================================================== scp_app_binary ==========================================================="/>		
		<echo message="==================================================================================================================================="/>	
	</target>

	<target name="scp_app_inst_log" depends="clock">
		<echo message="==================================================================================================================================="/>		
		<echo message="======================================================= scp_app_inst_log =========================================================="/>			
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>
		<echo message=""/>			
		<antcall target="scp_app_binary_inst_log"/>
		<antcall target="scp_deliverycode_inst_log"/>
		<echo message=""/>		
		<echo message="B_End: ${clock}"/>
		<echo message=""/>
		<echo message="======================================================= scp_app_inst_log =========================================================="/>			
		<echo message="==================================================================================================================================="/>		
	</target>

	<target name="scp_app_binary_inst_log">
		<!--echo message="==================================================================================================================================="/>
		<echo message="===================================================== scp_app_binary_inst_log ====================================================="/>
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>
		<echo message=""/-->		

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>		

		<antcall target="scp_to_source">
			<param name="hostdir" value="${app_patch_host_path}"/>
			<param name="hostfile" value="ceb_pack_${package_name}.log"/>
			<param name="sourcedir" location="${installation_logs}/${branch_name}/${package_name}/${host_name}"/>
			<param name="rusr" value="${apuser}"/>
			<param name="rpwd" value="${appasswd}"/>
			<param name="rhost" value="${aphost}"/>				
		</antcall>
		<!--echo message=""/>		
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="===================================================== scp_app_binary_inst_log ====================================================="/>
		<echo message="==================================================================================================================================="/-->
	</target>

	<target name="scp_deliverycode_inst_log">
		<!--echo message="==================================================================================================================================="/>
		<echo message="===================================================== scp_deliverycode_inst_log ==================================================="/>
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>		
		<echo message=""/-->
		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>
		<!--echo message="ceb_pack_${package_name}.log"/-->
		<!--loadfile property="file" srcfile="${build}/ceb_pack_${package_name}.lst"/-->
		<loadfile property="file" srcfile="${installation_logs}/${branch_name}/${package_name}/binaries/build/ceb_pack_${package_name}.lst"/>
		<foreach list="${file}" target="scp_deliverycode_inst_log_sub_process" param="lineitem" delimiter="${line.separator}" inheritall="true"/>
		<!--echo message=""/>			
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="===================================================== scp_deliverycode_inst_log ==================================================="/>
		<echo message="==================================================================================================================================="/-->
	</target>	

	<target name="scp_deliverycode_inst_log_sub_process" depends="clock">
		<echo message="B_Start: ${clock}"/>	

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>	

		<echo message="${lineitem}"/>
		<propertyregex property="deliverycode" input="${lineitem}" regexp="([^\_]*).([^\_]*)\s(f|p)" select="\2" override="true"/>
		<propertyregex property="module" input="${lineitem}" regexp="([^\_]*).([^\_]*)\s(f|p)" select="\1" override="true"/>
		<echo message="${deliverycode}"/>
		<echo message="${module}"/>
		<switch value="${module}">
			<case value="xxfe">
				<antcall target="scp_to_source">
					<param name="hostdir" value="${xxfetop_host_path}/install/${deliverycode}"/>
					<param name="hostfile" value="*setup*.log"/>
					<param name="sourcedir" location="${installation_logs}/${branch_name}/${package_name}/${host_name}"/>
					<param name="rusr" value="${apuser}"/>
					<param name="rpwd" value="${appasswd}"/>
					<param name="rhost" value="${aphost}"/>						
				</antcall>
			</case>
			<case value="xxfi">
				<antcall target="scp_to_source">
					<param name="hostdir" value="${xxfitop_host_path}/install/${deliverycode}"/>
					<param name="hostfile" value="*setup*.log"/>
					<param name="sourcedir" location="${installation_logs}/${branch_name}/${package_name}/${host_name}"/>
					<param name="rusr" value="${apuser}"/>
					<param name="rpwd" value="${appasswd}"/>
					<param name="rhost" value="${aphost}"/>					
				</antcall>
			</case>
		</switch>
		<echo message="B_End: ${clock}"/>		
	</target>

	<target name="scp_to_remote" depends="clock">
		<echo message="B_Start: ${clock}"/>		
		<scp Todir="${rusr}:${rpwd}@${rhost}:${hostdir}" trust="true">
			<fileset dir="${sourcedir}">
				<include name="${sourcefile}"/>
			</fileset>
		</scp>
		<echo message="B_End: ${clock}"/>		
	</target>

	<target name="scp_to_source" depends="clock">
		<echo message="B_Start: ${clock}"/>		
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<scp localTodir="${sourcedir}" remotefile="${rusr}:${rpwd}@${rhost}:${hostdir}/${hostfile}" trust="true"/>
		<echo message="B_End: ${clock}"/>		
	</target>	

	<!-- End of targets to move app binary -->

	<!-- Start of targets to move db binary -->

	<target name="scp_db_binary" depends="clock">	
		<echo message="==================================================================================================================================="/>		
		<echo message="======================================================== scp_db_binary ==========================================================="/>		
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>		
		<echo message=""/>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${db_build_property_file}"/>				
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>
		<propertycopy name="package_name" from="package.name"/>		
		<propertycopy name="db_package_name" from="db.package.name"/>


		<antcall target="scp_to_remote">
			<!--param name="sourcedir" value="${db_build}"/-->
			<param name="sourcedir" value="${installation_logs}/${branch_name}/${package_name}/${db_package_name}/binaries/db_build"/>
			<param name="sourcefile" value="*.tar.gz"/>
			<param name="hostdir" value="${db_patch_host_path}"/>
			<param name="rusr" value="${dbuser}"/>
			<param name="rpwd" value="${dbpasswd}"/>
			<param name="rhost" value="${dbhost}"/>
		</antcall>
		<echo message=""/>		
		<echo message="B_End: ${clock}"/>		
		<echo message=""/>
		<echo message="======================================================== scp_db_binary ==========================================================="/>		
		<echo message="==================================================================================================================================="/>	
	</target>	

	<target name="scp_db_inst_log">
		<echo message="==================================================================================================================================="/>
		<echo message="======================================================= scp_db_inst_log ==========================================================="/>
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>
		<echo message=""/>		
		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>		
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>				

		<antcall target="scp_to_source">
			<param name="hostdir" value="${db_patch_host_path}/xxfi_db"/>
			<param name="hostfile" value="log.log"/>
			<param name="sourcedir" location="${installation_logs}/db_installation/${branch_name}/${host_name}/"/>
			<param name="rusr" value="${dbuser}"/>
			<param name="rpwd" value="${dbpasswd}"/>
			<param name="rhost" value="${dbhost}"/>				
		</antcall>			

		<echo message=""/>		
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="======================================================= scp_db_inst_log ==========================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>

	<target name="scp_db_binary_inst_log">
		<echo message="==================================================================================================================================="/>
		<echo message="===================================================== scp_db_binary_inst_log ====================================================="/>
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>
		<echo message=""/>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>
		<loadproperties srcFile="${db_build_property_file}"/>				
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="db_package_name" from="db.package.name"/>

		<antcall target="scp_to_source">
			<param name="hostdir" value="${db_patch_host_path}/log"/>
			<!--param name="hostfile" value="*.log"/-->
			<param name="hostfile" value="cebdb_pack_${db_package_name}.log"/>			
			<param name="sourcedir" location="${installation_logs}/${branch_name}/${package_name}/${db_package_name}/${host_name}/"/>
			<param name="rusr" value="${dbuser}"/>
			<param name="rpwd" value="${dbpasswd}"/>
			<param name="rhost" value="${dbhost}"/>				
		</antcall>	

		<echo message=""/>		
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="===================================================== scp_db_binary_inst_log ====================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>	

	<!-- Start of targets to move db binary -->	

<!-- End of targets to move file -->


<!-- Start of targets to prepare-deploy -->		

	<target name="prepare_deploy" depends="clock">
		<echo message="==================================================================================================================================="/>
		<echo message="======================================================= prepare_deployment ========================================================"/>
		<echo message=""/>
		<echo message="B_Start: ${clock}"/>
		<echo message=""/>

		<antcall target="download_versionproperty_from_artifactory"/>

		<fail message="Oooops !!! version.properties is missing. Please check the input">
    		<condition>
        		<not>
            		<resourcecount count="1">
                		<fileset id="fs" dir="${properties}" includes="version.properties"/>
            		</resourcecount>
        		</not>
    		</condition>
		</fail>						

		<antcall target="build_branch"/>
		<antcall target="build_target"/>		
		<antcall target="build_type"/>
		<antcall target="set_variables"/>		
		<antcall target="list_affected_features"/>

		<loadproperties  srcFile="${tmp}/branch.properties"/>
		<!--loadproperties  srcFile="${tmp}/temp.properties"/>
		<loadproperties  srcFile="${tmp}/temp_db.properties"/-->

		<if>
			<equals arg1="${build_type}" arg2="f" casesensitive="No"/>
				<then>
					<antcall target="prepare_file_build_checkout_file"/>
				</then>
			<elseif>
				<equals arg1="${build_type}" arg2="m" casesensitive="No"/>
					<then>
						<antcall target="prepare_module_build_checkout_file"/>
					</then>
			</elseif>
			<else>
				<echo message="===================================== ! Alert ! =========================================="/>
				<echo message="${build_type} is an invalid build_type"/>
				<echo message="===================================== ! Alert ! =========================================="/>
			</else>
		</if>

		<property file="${tmp}/installation_to_include.properties"/>

		<if>
			<and>
				<equals arg1="${app_patch}" arg2="1"/>
				<equals arg1="${db_patch}" arg2="1"/>
			</and>
				<then>
					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="============================ Setting DB and App build number ============================="/>
					<echo message="===================================== ! Info ! ==========================================="/>

					<antcall target="set_db_buildnumber"/>						
					<antcall target="set_buildnumber"/>						

				</then>
		<elseif>
			<and>
				<equals arg1="${db_patch}" arg2="1"/>
			</and>
				<then>
					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="============================== Setting DB build number ==================================="/>
					<echo message="===================================== ! Info ! ==========================================="/>

					<antcall target="set_db_buildnumber"/>	
				</then>
		</elseif>
		<elseif>
			<and>
				<equals arg1="${app_patch}" arg2="1"/>
			</and>
				<then>
					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="============================== Setting APP build number =================================="/>
					<echo message="===================================== ! Info ! ==========================================="/>

					<antcall target="set_buildnumber"/>	

				</then>
		</elseif>
		</if>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<mkdir dir="${installation_logs}/${branch_name}"/>

		<antcall target="upload_versionproperty_to_artifactory"/>		

		<echo message=""/>
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="======================================================= prepare_deployment ========================================================"/>
		<echo message="==================================================================================================================================="/>
	</target>


	<target name="prepare_deploy_between_tags" depends="clock">
		<echo message="==================================================================================================================================="/>
		<echo message="=================================================== prepare_deploy_between_tags ==================================================="/>
		<echo message=""/>
		<echo message="B_Start: ${clock}"/>
		<echo message=""/>

		<antcall target="download_versionproperty_from_artifactory"/>

		<fail message="Oooops !!! version.properties is missing. Please check the input">
    		<condition>
        		<not>
            		<resourcecount count="1">
                		<fileset id="fs" dir="${properties}" includes="version.properties"/>
            		</resourcecount>
        		</not>
    		</condition>
		</fail>

		<antcall target="build_branch"/>
		<antcall target="build_between_tags"/>
		<antcall target="build_target"/>		
		<antcall target="build_type"/>
		<antcall target="set_variables"/>		
		<antcall target="list_affected_features_between_tags"/>

		<loadproperties  srcFile="${tmp}/branch.properties"/>
		<!--loadproperties  srcFile="${tmp}/temp.properties"/>
		<loadproperties  srcFile="${tmp}/temp_db.properties"/-->

		<if>
			<equals arg1="${build_type}" arg2="f" casesensitive="No"/>
				<then>
					<antcall target="prepare_file_build_checkout_file"/>
				</then>
			<elseif>
				<equals arg1="${build_type}" arg2="m" casesensitive="No"/>
					<then>
						<antcall target="prepare_module_build_checkout_file"/>
					</then>
			</elseif>
			<else>
				<echo message="===================================== ! Alert ! =========================================="/>
				<echo message="${build_type} is an invalid build_type"/>
				<echo message="===================================== ! Alert ! =========================================="/>
			</else>
		</if>

		<property file="${tmp}/installation_to_include.properties"/>

		<if>
			<and>
				<equals arg1="${app_patch}" arg2="1"/>
				<equals arg1="${db_patch}" arg2="1"/>
			</and>
				<then>
					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="============================ Setting DB and App build number ============================="/>
					<echo message="===================================== ! Info ! ==========================================="/>

					<antcall target="set_db_buildnumber"/>						
					<antcall target="set_buildnumber"/>						

				</then>
		<elseif>
			<and>
				<equals arg1="${db_patch}" arg2="1"/>
			</and>
				<then>
					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="=============================== Setting DB build number =================================="/>
					<echo message="===================================== ! Info ! ==========================================="/>

					<antcall target="set_db_buildnumber"/>	
				</then>
		</elseif>
		<elseif>
			<and>
				<equals arg1="${app_patch}" arg2="1"/>
			</and>
				<then>
					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="=============================== Setting APP build number ================================="/>
					<echo message="===================================== ! Info ! ==========================================="/>

					<antcall target="set_buildnumber"/>	

				</then>
		</elseif>
		</if>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<mkdir dir="${installation_logs}/${branch_name}"/>

		<antcall target="upload_versionproperty_to_artifactory"/>		

		<echo message=""/>
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="=================================================== prepare_deploy_between_tags ==================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>	

	<target name="prepare_deploy_between_autotags" depends="clock">
		<echo message="===================================================================================================================================="/>
		<echo message="================================================= prepare_deploy_between_autotags =================================================="/>
		<echo message=""/>
		<echo message="B_Start: ${clock}"/>
		<echo message=""/>

		<antcall target="download_versionproperty_from_artifactory"/>

		<fail message="Oooops !!! version.properties is missing. Please check the input">
    		<condition>
        		<not>
            		<resourcecount count="1">
                		<fileset id="fs" dir="${properties}" includes="version.properties"/>
            		</resourcecount>
        		</not>
    		</condition>
		</fail>								

		<antcall target="build_branch"/>
		<antcall target="build_target"/>		
		<antcall target="build_type"/>
		<antcall target="set_variables"/>		
		<antcall target="list_affected_features_between_autotags"/>

		<loadproperties  srcFile="${tmp}/branch.properties"/>
		<!--loadproperties  srcFile="${tmp}/temp.properties"/>
		<loadproperties  srcFile="${tmp}/temp_db.properties"/-->

		<if>
			<equals arg1="${build_type}" arg2="f" casesensitive="No"/>
				<then>
					<antcall target="prepare_file_build_checkout_file"/>
				</then>
			<elseif>
				<equals arg1="${build_type}" arg2="m" casesensitive="No"/>
					<then>
						<antcall target="prepare_module_build_checkout_file"/>
					</then>
			</elseif>
			<else>
				<echo message="===================================== ! Alert ! =========================================="/>
				<echo message="${build_type} is an invalid build_type"/>
				<echo message="===================================== ! Alert ! =========================================="/>
			</else>
		</if>

		<property file="${tmp}/installation_to_include.properties"/>

		<if>
			<and>
				<equals arg1="${app_patch}" arg2="1"/>
				<equals arg1="${db_patch}" arg2="1"/>
			</and>
				<then>
					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="============================ Setting DB and App build number ============================="/>
					<echo message="===================================== ! Info ! ==========================================="/>

					<antcall target="set_db_buildnumber"/>						
					<antcall target="set_buildnumber"/>						

				</then>
		<elseif>
			<and>
				<equals arg1="${db_patch}" arg2="1"/>
			</and>
				<then>
					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="============================== Setting DB build number ==================================="/>
					<echo message="===================================== ! Info ! ==========================================="/>

					<antcall target="set_db_buildnumber"/>	
				</then>
		</elseif>
		<elseif>
			<and>
				<equals arg1="${app_patch}" arg2="1"/>
			</and>
				<then>
					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="============================== Setting APP build number =================================="/>
					<echo message="===================================== ! Info ! ==========================================="/>

					<antcall target="set_buildnumber"/>	

				</then>
		</elseif>
		</if>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<mkdir dir="${installation_logs}/${branch_name}"/>

		<antcall target="upload_versionproperty_to_artifactory"/>				

		<echo message=""/>
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="================================================= prepare_deploy_between_autotags =================================================="/>
		<echo message="===================================================================================================================================="/>
	</target>	

	<target name="prepare_deploy_on_demand" depends="clock">
		<echo message="==================================================================================================================================="/>
		<echo message="=================================================== prepare_deploy_on_demand ======================================================"/>
		<echo message=""/>
		<echo message="B_Start: ${clock}"/>
		<echo message=""/>

		<antcall target="download_versionproperty_from_artifactory"/>

		<fail message="Oooops !!! version.properties is missing. Please check the input">
    		<condition>
        		<not>
            		<resourcecount count="1">
                		<fileset id="fs" dir="${properties}" includes="version.properties"/>
            		</resourcecount>
        		</not>
    		</condition>
		</fail>	

		<antcall target="build_branch"/>				
		<antcall target="build_target"/>	
		<antcall target="build_type"/>		
		<antcall target="set_variables"/>
		<antcall target="branch_checkout"/>	

		<loadproperties  srcFile="${tmp}/branch.properties"/>

		<shellscript shell="bash">
				echo -ne "\n"
				echo -e "\e[31m Please enter the xxfi delivery codes to deploy seperated by "\;" \e[0m"
				echo -e "\e[96m Example: i354;i658 \e[0m"
				echo -e "\e[93m To skip build of xxfi delivery codes, click return with no value \e[0m"
				echo -ne "\n"
		</shellscript>
		<input message="xxfi delivery codes to build - " addProperty="xxfi_delivery_codes"/>
		<shellscript shell="bash">
				echo -ne "\n"
				echo -e "\e[31m Please enter the xxfe delivery codes to deploy seperated by "\;" \e[0m"
				echo -e "\e[96m Example: i354;i658 \e[0m"
				echo -e "\e[93m To skip build of xxfe delivery codes, click return with no value \e[0m"
				echo -ne "\n"
		</shellscript>		
		<input message="xxfe delivery codes to build - " addProperty="xxfe_delivery_codes"/>
		<shellscript shell="bash">
				echo -ne "\n"
				echo -e "\e[31m Please enter the xxfi_db delivery codes to deploy seperated by "\;" \e[0m"
				echo -e "\e[96m Example: i354;i658 \e[0m"
				echo -e "\e[93m To skip build of xxfi_db delivery codes, click return with no value \e[0m"
				echo -ne "\n"
		</shellscript>		
		<input message="xxfi_db delivery codes to build - " addProperty="xxfi_db_delivery_codes"/>

		<echo message=""/>
		<shellscript shell="bash">
				./utility/build_on_demand.sh "${xxfi_delivery_codes}" "${xxfe_delivery_codes}" "${xxfi_db_delivery_codes}"
		</shellscript>
		<echo message=""/>	

		<loadproperties  srcFile="${tmp}/branch.properties"/>

		<if>
			<equals arg1="${build_type}" arg2="f" casesensitive="No"/>
				<then>
					<antcall target="prepare_file_build_checkout_file"/>
				</then>
			<elseif>
				<equals arg1="${build_type}" arg2="m" casesensitive="No"/>
					<then>
						<antcall target="prepare_module_build_checkout_file"/>
					</then>
			</elseif>
			<else>
				<echo message="===================================== ! Alert ! =========================================="/>				
				<echo message="${build_type} is an invalid build_type"/>
				<echo message="===================================== ! Alert ! =========================================="/>
			</else>
		</if>

		<property file="${tmp}/installation_to_include.properties"/>

		<if>
			<and>
				<equals arg1="${app_patch}" arg2="1"/>
				<equals arg1="${db_patch}" arg2="1"/>
			</and>
				<then>
					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="============================ Setting DB and App build number ============================="/>
					<echo message="===================================== ! Info ! ==========================================="/>

					<antcall target="set_db_buildnumber"/>						
					<antcall target="set_buildnumber"/>						

				</then>
		<elseif>
			<and>
				<equals arg1="${db_patch}" arg2="1"/>
			</and>
				<then>
					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="============================== Setting DB build number ==================================="/>
					<echo message="===================================== ! Info ! ==========================================="/>

					<antcall target="set_db_buildnumber"/>	
				</then>
		</elseif>
		<elseif>
			<and>
				<equals arg1="${app_patch}" arg2="1"/>
			</and>
				<then>

					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="============================== Setting APP build number =================================="/>
					<echo message="===================================== ! Info ! ==========================================="/>

					<antcall target="set_buildnumber"/>	

				</then>
		</elseif>
		</if>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<mkdir dir="${installation_logs}/${branch_name}"/>

		<antcall target="upload_versionproperty_to_artifactory"/>					

		<echo message=""/>
		<echo message="B_End: ${clock}"/>

		<echo message=""/>				
		<echo message="=================================================== prepare_deploy_on_demand ======================================================"/>
		<echo message="==================================================================================================================================="/>
	</target>	

	<target name="build_branch">
		<!--echo message="==================================================================================================================================="/>
		<echo message="=========================================================== build_branch =========================================================="/-->
		<echo message=""/>

		<property name="branch_name" value="not_set_from_command_line"/>

		<if>
			<equals arg1="${branch_name}" arg2="not_set_from_command_line" casesensitive="No"/>
				<then>
					<var name="branch_name" unset="true"/>
					<input message="Please enter the branch name?" addProperty="branch_name"/>
				</then>
		</if>

		<if>
			<not>
				<equals arg1="${branch_name}" arg2="not_set_from_command_line" casesensitive="No"/>
			</not>
				<then>
					<shellscript shell="bash">
			    		rm -f "${branch_to_build_property_file}"
			    		echo "git_branch="${branch_name} > "${branch_to_build_property_file}"
			    		echo ""
			    		echo "============================== branch included for build ==========================="
			    		echo ""
			    		cat "${branch_to_build_property_file}"
			    		echo ""
			    		echo "============================== branch included for build ==========================="
			    		echo ""
					</shellscript>

				</then>
		</if>
		<echo message=""/>					
		<!--echo message="=========================================================== build_branch =========================================================="/>
		<echo message="==================================================================================================================================="/-->
	</target>	


	<target name="build_between_tags">
		<!--echo message="==================================================================================================================================="/>
		<echo message="======================================================= build_between_tags ==========================================================="/-->
		<echo message=""/>

		<property name="build_tag1" value="not_set_from_command_line"/>

		<if>
			<equals arg1="${build_tag1}" arg2="not_set_from_command_line" casesensitive="No"/>
				<then>
					<var name="build_tag1" unset="true"/>
					<input message="Please enter tag#1?" addProperty="build_tag1"/>
				</then>		
		</if>

		<property name="build_tag2" value="not_set_from_command_line"/>

		<if>
			<equals arg1="${build_tag2}" arg2="not_set_from_command_line" casesensitive="No"/>
				<then>
					<var name="build_tag2" unset="true"/>
					<input message="Please enter tag#2?" addProperty="build_tag2"/>
				</then>		
		</if>

		<if>
			<not>
				<equals arg1="${branch_name}" arg2="not_set_from_command_line" casesensitive="No"/>
			</not>
				<then>
					<shellscript shell="bash">

							<!--echo "build_type="${build_type_temp_var} >> "${branch_to_build_property_file}"/-->		
						    ./utility/setprop.sh "build_tag1" "${build_tag1}" "${branch_to_build_property_file}"
					</shellscript>					
				</then>
		</if>

		<if>
			<not>
				<equals arg1="${branch_name}" arg2="not_set_from_command_line" casesensitive="No"/>
			</not>
				<then>
					<shellscript shell="bash">

							<!--echo "build_type="${build_type_temp_var} >> "${branch_to_build_property_file}"/-->		
						    ./utility/setprop.sh "build_tag2" "${build_tag2}" "${branch_to_build_property_file}"
						    echo ""
			    			echo "============================== tags included for build ==========================="
			    			echo ""
			    			cat "${branch_to_build_property_file}"
			    			echo ""
			    			echo "============================== tags included for build ==========================="
			    			echo ""
					</shellscript>					
				</then>
		</if>		

		<echo message=""/>
		<!--echo message="=========================================================== build_between_tags =========================================================="/>
		<echo message="==================================================================================================================================="/-->
	</target>


	<target name="build_target">
		<!--echo message="==================================================================================================================================="/>
		<echo message="=========================================================== build_target =========================================================="/-->
		<echo message=""/>

		<property name="build_target_temp_var" value="not_set_from_command_line"/>

		<if>
			<equals arg1="${build_target_temp_var}" arg2="not_set_from_command_line" casesensitive="No"/>
				<then>
					<var name="build_target_temp_var" unset="true"/>
					<input message="Please enter if build should be done for the prod(p) or test(t)?" addProperty="build_target_temp_var"/>
				</then>		
		</if>


		<if>
			<or>
				<equals arg1="${build_target_temp_var}" arg2="p" casesensitive="No"/>
				<equals arg1="${build_target_temp_var}" arg2="t" casesensitive="No"/>
			</or>
				<then>
					<shellscript shell="bash">

							<!--echo "build_type="${build_type_temp_var} >> "${branch_to_build_property_file}"/-->		
						    ./utility/setprop.sh "build_target" "${build_target_temp_var}" "${branch_to_build_property_file}"
						    echo ""
			    			echo "============================== branch included for build ==========================="
			    			echo ""
			    			cat "${branch_to_build_property_file}"
			    			echo ""
			    			echo "============================== branch included for build ==========================="
			    			echo ""
					</shellscript>					
				</then>
			<else>
				<echo message=""/>				
				<echo message="===================================== ! Alert ! =========================================="/>
				<echo message="Supplied value for build_target: ${build_target_temp_var}"/>				
				<echo message="Expected prod(p) or test(t)"/>
				<echo message="===================================== ! Alert ! =========================================="/>
				<echo message=""/>							
			</else>
		</if>

		<echo message=""/>
		<!--echo message="=========================================================== build_target =========================================================="/>
		<echo message="==================================================================================================================================="/-->
	</target>


	<target name="build_type">
		<!--echo message="==================================================================================================================================="/>
		<echo message="=========================================================== build_type ============================================================"/-->
		<echo message=""/>

		<property name="build_type_temp_var" value="not_set_from_command_line"/>

		<if>
			<equals arg1="${build_type_temp_var}" arg2="not_set_from_command_line" casesensitive="No"/>
				<then>
					<var name="build_type_temp_var" unset="true"/>
					<input message="Please enter if build should be done for the file(f) or module(m)?" addProperty="build_type_temp_var"/>
				</then>		
		</if>

		<if>
			<or>
				<equals arg1="${build_type_temp_var}" arg2="f" casesensitive="No"/>
				<equals arg1="${build_type_temp_var}" arg2="m" casesensitive="No"/>
			</or>
				<then>
					<shellscript shell="bash">

							<!--echo "build_type="${build_type_temp_var} >> "${branch_to_build_property_file}"/-->		
						    ./utility/setprop.sh "build_type" "${build_type_temp_var}" "${branch_to_build_property_file}"
						    echo ""
						    echo "============================== branch included for build ==========================="
						    echo ""
						    cat "${branch_to_build_property_file}"
						    echo ""
						    echo "============================== branch included for build ==========================="
						    echo ""
					</shellscript>					
				</then>
			<else>
				<echo message="===================================== ! Alert ! =========================================="/>
				<echo message="Supplied value for build_type: ${build_type_temp_var}"/>				
				<echo message="Expected file(f) or module(m)"/>
				<echo message="===================================== ! Alert ! =========================================="/>	
			</else>
		</if>

		<echo message=""/>
		<!--echo message="=========================================================== build_type ============================================================"/>
		<echo message="==================================================================================================================================="/-->
	</target>

	<target name="set_buildnumber">
		<!--echo message="==================================================================================================================================="/>
		<echo message="=========================================================== set_buildnumber ======================================================="/-->
		<echo message=""/>

		<loadproperties  srcFile="${tmp}/branch.properties"/>

		<if>
			<equals arg1="${build_target}" arg2="t" casesensitive="No"/>
				<then>
					<shellscript shell="bash">

						branch_name=`grep 'git_branch' "${branch_to_build_property_file}" | cut -d '=' -f 2`;
						file_name=`grep $branch_name"-TestBuildNumber" "${branch_version_property_file}" | cut -d '=' -f 1`;
						echo $file_name;

						if [ "$file_name" == "" ];
							then

								echo ""
								echo "================================== ! Info ! ======================================="
								echo ""

								echo "Branch does not exist, creating new entry"

								./utility/setprop.sh "$branch_name"-TestBuildNumber"" 1 "${branch_version_property_file}";
								grep "$branch_name"-TestBuildNumber"" "${branch_version_property_file}";

								echo ""
								echo "================================== ! Info ! ======================================="
								echo ""

						else
								build_number=`grep $branch_name"-TestBuildNumber" "${branch_version_property_file}" | cut -d '=' -f 2`;
								echo "Recent build number: "$build_number;

								build_number=`expr $build_number + 1`;
								echo "New build number: "$build_number;

								./utility/setprop.sh "$branch_name"-TestBuildNumber"" "$build_number" "${branch_version_property_file}"
						fi

					</shellscript>					
				</then>
			<elseif>
				<equals arg1="${build_target}" arg2="p" casesensitive="No"/>
					<then>
						<shellscript shell="bash">

							branch_name=`grep 'git_branch' "${branch_to_build_property_file}" | cut -d '=' -f 2`;
							file_name=`grep $branch_name"-ProdBuildNumber" "${branch_version_property_file}" | cut -d '=' -f 1`;
							echo $file_name;

							if [ "$file_name" == "" ];
								then 
									echo ""
									echo "================================== ! Info ! ======================================="
									echo ""

									echo "Branch does not exist, creating new entry"

									./utility/setprop.sh "$branch_name"-ProdBuildNumber"" 1 "${branch_version_property_file}";
									grep "$branch_name"-ProdBuildNumber"" "${branch_version_property_file}";

									echo ""
									echo "================================== ! Info ! ======================================="
									echo ""

							else
									build_number=`grep $branch_name"-ProdBuildNumber" "${branch_version_property_file}" | cut -d '=' -f 2`;
									echo "Recent build number: "$build_number;

									build_number=`expr $build_number + 1`;
									echo "New build number: "$build_number;

									./utility/setprop.sh "$branch_name"-ProdBuildNumber"" "$build_number" "${branch_version_property_file}";

							fi

						</shellscript>
					</then>
			</elseif>
			<else>
				<echo message="===================================== ! Alert ! =========================================="/>
				<echo message="${build_target} is an invalid build_target"/>
				<echo message="===================================== ! Alert ! =========================================="/>
			</else>
		</if>

		<echo message=""/>
		<!--echo message="=========================================================== set_buildnumber ======================================================="/>
		<echo message="==================================================================================================================================="/-->
	</target>	


	<target name="set_db_buildnumber">
		<!--echo message="==================================================================================================================================="/>
		<echo message="=========================================================== set_db_buildnumber ======================================================="/-->
		<echo message=""/>

		<loadproperties  srcFile="${tmp}/branch.properties"/>

		<if>
			<equals arg1="${build_target}" arg2="t" casesensitive="No"/>
				<then>
					<shellscript shell="bash">

						branch_name=`grep 'git_branch' "${branch_to_build_property_file}" | cut -d '=' -f 2`;
						file_name=`grep $branch_name"-TestBuildNumber-DB" "${branch_version_property_file}" | cut -d '=' -f 1`;
						echo $file_name;

						if [ "$file_name" == "" ];
							then

								echo ""
								echo "================================== ! Info ! ======================================="
								echo ""

								echo "Branch does not exist, creating new entry"

								./utility/setprop.sh "$branch_name"-TestBuildNumber-DB"" 1 "${branch_version_property_file}";
								grep "$branch_name"-TestBuildNumber-DB"" "${branch_version_property_file}";

								echo ""
								echo "================================== ! Info ! ======================================="
								echo ""

						else
								build_number=`grep $branch_name"-TestBuildNumber-DB" "${branch_version_property_file}" | cut -d '=' -f 2`;
								echo "Recent build number: "$build_number;

								build_number=`expr $build_number + 1`;
								echo "New build number: "$build_number;

								./utility/setprop.sh "$branch_name"-TestBuildNumber-DB"" "$build_number" "${branch_version_property_file}"
						fi

					</shellscript>					
				</then>
			<elseif>
				<equals arg1="${build_target}" arg2="p" casesensitive="No"/>
					<then>
						<shellscript shell="bash">

							branch_name=`grep 'git_branch' "${branch_to_build_property_file}" | cut -d '=' -f 2`;
							file_name=`grep $branch_name"-ProdBuildNumber-DB" "${branch_version_property_file}" | cut -d '=' -f 1`;
							echo $file_name;

							if [ "$file_name" == "" ];
								then 
									echo ""
									echo "================================== ! Info ! ======================================="
									echo ""

									echo "Branch does not exist, creating new entry"

									./utility/setprop.sh "$branch_name"-ProdBuildNumber-DB"" 1 "${branch_version_property_file}";
									grep "$branch_name"-ProdBuildNumber-DB"" "${branch_version_property_file}";

									echo ""
									echo "================================== ! Info ! ======================================="
									echo ""

							else
									build_number=`grep $branch_name"-ProdBuildNumber-DB" "${branch_version_property_file}" | cut -d '=' -f 2`;
									echo "Recent build number: "$build_number;

									build_number=`expr $build_number + 1`;
									echo "New build number: "$build_number;

									./utility/setprop.sh "$branch_name"-ProdBuildNumber-DB"" "$build_number" "${branch_version_property_file}";

							fi

						</shellscript>
					</then>
			</elseif>
			<else>
				<echo message="===================================== ! Alert ! =========================================="/>
				<echo message="${build_target} is an invalid build_target"/>
				<echo message="===================================== ! Alert ! =========================================="/>
			</else>
		</if>

		<echo message=""/>
		<!--echo message="========================================================== set_db_buildnumber ======================================================"/>
		<echo message="==================================================================================================================================="/-->
	</target>		

	<target name="list_environments" depends="clock">
		<echo message="==================================================================================================================================="/>
		<echo message="======================================================== available_environments ==================================================="/>		
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>		
		<echo message=""/>

		<propertyselector property="environment.index.list" delimiter="," match="env\.([^\.]*)\.hostname" select="\1" casesensitive="false"/>
		<sortlist property="environment.index.list.sorted" value="${environment.index.list}" numeric="true" delimiter=","/>
		<foreach list="${environment.index.list.sorted}" target="print_name" param="index"/>

		<echo message=""/>		
		<echo message="B_End: ${clock}"/>		
		<echo message=""/>		
		<echo message="======================================================== available_environments ==================================================="/>
		<echo message="==================================================================================================================================="/>	
	</target>

	<target name="print_name" description="Displays available environments">
		<echo message="env.${index}.hostname"/>
		<propertycopy name="environment" from="env.${index}.hostname"/>
		<echo message="${index}# ${environment}"/>
	</target>

	<target name="set_variables" depends="list_environments">
		<!--echo message="==================================================================================================================================="/>
		<echo message="========================================================== set_variables =========================================================="/-->		
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>		
		<echo message=""/>			

		<property name="host.choice" value="not_set_from_command_line"/>

		<if>
			<equals arg1="${host.choice}" arg2="not_set_from_command_line" casesensitive="No"/>
				<then>
					<var name="host.choice" unset="true"/>
					<input message="Please select the target environment .." addProperty="host.choice"/>
				</then>		
		</if>

		<propertycopy name="hostname" from="env.${host.choice}.hostname"/>
 		<propertyfile file="${active_deploy_env_property_file}">
 			<entry key="hostname" value="${hostname}"/>
 		</propertyfile>			
		<propertycopy name="aphost" from="env.${host.choice}.hostaddress"/>
 		<propertyfile file="${active_deploy_env_property_file}">
 			<entry key="aphost" value="${aphost}"/>
 		</propertyfile>			
		<propertycopy name="dbhost" from="env.${host.choice}.dbhostaddress"/>
 		<propertyfile file="${active_deploy_env_property_file}">
 			<entry key="dbhost" value="${dbhost}"/>
 		</propertyfile>		
		<propertycopy name="dbport" from="env.${host.choice}.dbport"/>
 		<propertyfile file="${active_deploy_env_property_file}">
 			<entry key="dbport" value="${dbport}"/>
 		</propertyfile>			
		<propertycopy name="dbsid" from="env.${host.choice}.dbservicename"/>
 		<propertyfile file="${active_deploy_env_property_file}">
 			<entry key="dbsid" value="${dbsid}"/>
 		</propertyfile>			
		<propertycopy name="apuser" from="env.${host.choice}.user"/>
 		<propertyfile file="${active_deploy_env_property_file}">
 			<entry key="apuser" value="${apuser}"/>
 		</propertyfile>		
		<propertycopy name="appasswd" from="env.${host.choice}.passwd"/>
 		<propertyfile file="${active_deploy_env_property_file}">
 			<entry key="appasswd" value="${appasswd}"/>
 		</propertyfile>			
		<propertycopy name="snum" from="env.${host.choice}.servnum"/>
 		<propertyfile file="${active_deploy_env_property_file}">
 			<entry key="snum" value="${snum}"/>
 		</propertyfile>			
		<propertycopy name="dbuser" from="env.${host.choice}.dbuser"/>
 		<propertyfile file="${active_deploy_env_property_file}">
 			<entry key="dbuser" value="${dbuser}"/>
 		</propertyfile>			
		<propertycopy name="dbpasswd" from="env.${host.choice}.dbpasswd"/>
 		<propertyfile file="${active_deploy_env_property_file}">
 			<entry key="dbpasswd" value="${dbpasswd}"/>
 		</propertyfile>			
		<propertycopy name="appspasswd" from="env.${host.choice}.appspasswd"/>
 		<propertyfile file="${active_deploy_env_property_file}">
 			<entry key="appspasswd" value="${appspasswd}"/>
 		</propertyfile>			
		<propertycopy name="schemapasswd" from="env.${host.choice}.schemapasswd"/>
 		<propertyfile file="${active_deploy_env_property_file}">
 			<entry key="schemapasswd" value="${schemapasswd}"/>
 		</propertyfile>			
		<propertycopy name="tomcatport" from="env.${host.choice}.tomcatport"/>
 		<propertyfile file="${active_deploy_env_property_file}">
 			<entry key="tomcatport" value="${tomcatport}"/>
 		</propertyfile>			
		<propertycopy name="weblogicpasswd" from="env.${host.choice}.weblogicpasswd"/>
 		<propertyfile file="${active_deploy_env_property_file}">
 			<entry key="weblogicpasswd" value="${weblogicpasswd}"/>
 		</propertyfile>
		<propertycopy name="applicationuser" from="env.${host.choice}.applicationuser"/>
 		<propertyfile file="${active_deploy_env_property_file}">
 			<entry key="applicationuser" value="${applicationuser}"/>
 		</propertyfile>			
		<propertycopy name="applicationpasswd" from="env.${host.choice}.applicationpasswd"/>
 		<propertyfile file="${active_deploy_env_property_file}">
 			<entry key="applicationpasswd" value="${applicationpasswd}"/>
 		</propertyfile>
<!-- Note: Known problem, ANT automatically escapes ':' while reading the application url value from scp.properties -->
		<propertycopy name="applicationurl" from="env.${host.choice}.applicationurl"/>
 		<propertyfile file="${active_deploy_env_property_file}">
 			<entry key="applicationurl" value="${applicationurl}"/>
 		</propertyfile>
		<echo message=""/>
 		<!--shellscript shell="bash">
 				echo "$applicationurl";
			    applicationurl=`grep applicationurl "${active_deploy_env_property_file}" | cut -d '=' -f 2`;
			    applicationurl=`echo "$applicationurl" | tr -d '\\'`;
			    echo "$applicationurl";
		</shellscript-->

		<echo message="================================================== Properties of the chosen environment ==========================================="/>
		<echo message=""/>		
		<shellscript shell="bash">
			    cat "${active_deploy_env_property_file}"  			
		</shellscript> 
		<echo message=""/>		
		<echo message="================================================== Properties of the chosen environment ==========================================="/>	

		<echo message=""/>		
		<echo message="B_End: ${clock}"/>		
		<echo message=""/>
		<!--echo message="========================================================== set_variables =========================================================="/>
		<echo message="==================================================================================================================================="/-->
	</target>

	<target name="prepare_module_build_checkout_file">
		<shellscript shell="bash">
				rm -f "${app_build_checkout_file}"
				rm -f "${db_build_checkout_file}"
				rm -f "${jar_build_checkout_file}"								
			    sh "${prepare_module_build_checkout_file}"
		</shellscript>
	</target>

	<target name="prepare_file_build_checkout_file">
		<shellscript shell="bash">
				rm -f "${app_build_checkout_file}"
				rm -f "${db_build_checkout_file}"
				rm -f "${jar_build_checkout_file}"
			    bash "${prepare_file_build_checkout_file}"
		</shellscript>
	</target>

	<target name="app_build_checkout">
		<shellscript shell="bash">
			    sh "${app_build_checkout_file}"
		</shellscript>
	</target>

	<target name="db_build_checkout">
		<shellscript shell="bash">
			    sh "${db_build_checkout_file}"
		</shellscript>
	</target>

	<target name="jar_build_checkout">
		<shellscript shell="bash">
			    sh "${jar_build_checkout_file}"
		</shellscript>
	</target>

	<target name="db_install_build_checkout">
		<shellscript shell="bash">
			    sh "${db_install_build_checkout_file}"
		</shellscript>
	</target>		

<!-- End of targets to prepare-deploy -->			

<!-- Start of targets to prepare-db-installation -->		

	<target name="prepare_db_installation" depends="clock">	
		<echo message="==================================================================================================================================="/>
		<echo message="======================================================= prepare_db_installation ==================================================="/>
		<echo message=""/>
		<echo message="B_Start: ${clock}"/>
		<echo message=""/>

		<antcall target="build_branch"/>
		<antcall target="branch_checkout"/>		
		<antcall target="set_variables"/>		
		<antcall target="db_install_build_checkout"/>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>

		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>	

		<!-- aaa -->

		<mkdir dir="${installation_logs}/db_installation/${branch_name}/${host_name}"/>

<!-- Preparing db_installation_index -->

		<echo message="================================================= Preparing db_installation_index ================================================="/>

		<shellscript shell="bash">
			
				echo --------------------------------------------------
				echo -------------setting module.properties------------

			    cp "${utility_space}"/modules.properties "${db_build}"/xxfi_db/

				echo --------------------------------------------------

				echo --------------------------------------------------
				echo ------setting installation_status.properties------

				touch "${installation_status_property_file}"

				echo -e "#""$(Date)" >"${installation_status_property_file}"

			    ./utility/setprop.sh "db_install_complete" 8 "${installation_status_property_file}" 			    

				echo --------------------------------------------------				

		</shellscript>		

<!-- End of preparing db_installation_index -->		

<!-- Preparing xxfi_db/build.xml -->

		<echo message=""/>
		<echo message="===================================== ! Info ! ==========================================="/>
		<echo message=""/>		

		<echo message="========================== Preparing db_installation_index ==============================="/>

		<!-- Original regex for reference "(<)(property\sname)(\=\")(oracle_sid)(\"\s)(value)(\=\")(ENV)(\"\s\/>)" -->

		<loadproperties srcFile="${active_deploy_env_property_file}"/>		

		<!--loadfile property="file" srcfile="${db_build}/xxfi_db/build.xml"/>	

 		<propertyregex property="x" input="${file}" regexp="(\&lt;)(property\sname)(\=\&quot;)(oracle_sid)(\&quot;\s)(value)(\=\&quot;)(ENV)(\&quot;\s/&gt;)" select="\8" override="true"/>
 		<echo message="value of x is ${x}"/-->			

		<echo message="============================ Preparing xxfi_db/build.xml ================================="/> 			

		<replaceregexp file="${db_build}/xxfi_db/build.xml"
               match="(\&lt;)(property\sname)(\=\&quot;)(oracle_sid)(\&quot;\s)(value)(\=\&quot;)(ENV)(\&quot;\s/&gt;)"
               replace="\1\2\3\4\5\6\7${dbsid}\9"
               byline="true"/>

		<!--loadfile property="file2" srcfile="${db_build}/xxfi_db/build.xml"/>	

 		<propertyregex property="y" input="${file2}" regexp="(\&lt;)(property\sname)(\=\&quot;)(oracle_sid)(\&quot;\s)(value)(\=\&quot;)(${dbsid})(\&quot;\s/&gt;)" select="\8" override="true"/>
 		<echo message="value of x is ${y}"/-->	               

		<replaceregexp file="${db_build}/xxfi_db/build.xml"
               match="(\&lt;)(property\sname)(\=\&quot;)(schema_password)(\&quot;\s)(value)(\=\&quot;)(SCHEMA_PASSWD)(\&quot;\s/&gt;)"
               replace="\1\2\3\4\5\6\7${schemapasswd}\9"
               byline="true"/>

		<replaceregexp file="${db_build}/xxfi_db/build.xml"
               match="(\&lt;)(property\sname)(\=\&quot;)(apps_password)(\&quot;\s)(value)(\=\&quot;)(APPS_PASSWD)(\&quot;\s/&gt;)"
               replace="\1\2\3\4\5\6\7${appspasswd}\9"
               byline="true"/>      

		<!-- Original regex for reference (\$\{schema_password\})(\@)(\$\{oracle_sid\})-->

		<!--loadfile property="file" srcfile="${db_build}/xxfi_db/build.xml"/>	

 		<propertyregex property="x" input="${file}" regexp="(\$\{schema_password\})(\@)(\$\{oracle_sid\})" select="\1 \3" override="true"/>
 		<echo message="value of x is ${x}"/-->		


		<replaceregexp file="${db_build}/xxfi_db/build.xml"
               match="(\$\{schema_password\})(\@)(\$\{oracle_sid\})"
               replace="${schemapasswd}\2${dbsid}"
               byline="true"/>                             

		<!--loadfile property="file2" srcfile="${db_build}/xxfi_db/build.xml"/>	               

 		<propertyregex property="x" input="${file2}" regexp="(${schemapasswd})(\@)(${dbsid})" select="\1 \3" override="true"/>
 		<echo message="value of x is ${x}"/-->			

<!-- End of preparing xxfi_db/build.xml -->

<!-- Preparing xxfi_db/i209/build.xml -->

		<echo message="========================== Preparing xxfi_db/i209/build.xml =============================="/>

		<!-- Original regex for reference (\$\{apps_password\})(\@)(\$\{oracle_sid\})-->

		<!--loadfile property="file" srcfile="${db_build}/xxfi_db/i209/build.xml"/>	

 		<propertyregex property="x" input="${file}" regexp="(\$\{apps_password\})(\@)(\$\{oracle_sid\})" select="\1 \3" override="true"/>
 		<echo message="value of x is ${x}"/-->		

		<replaceregexp file="${db_build}/xxfi_db/i209/build.xml"
               match="(\$\{apps_password\})(\@)(\$\{oracle_sid\})"
               replace="${appspasswd}\2${dbsid}"
               byline="true"/>                             

		<!--loadfile property="file2" srcfile="${db_build}/xxfi_db/i209/build.xml"/>	               

 		<propertyregex property="x" input="${file2}" regexp="(${appspasswd})(\@)(${dbsid})" select="\1 \3" override="true"/>
 		<echo message="value of x is ${x}"/-->			

<!-- End of preparing xxfi_db/i209/build.xml -->

<!-- Preparing xxfi_db/i320/build.xml -->

		<echo message="========================== Preparing xxfi_db/i320/build.xml =============================="/>

		<!-- Original regex for reference (\$\{schema_password\})(\@)(\$\{oracle_sid\})-->

		<!--loadfile property="file" srcfile="${db_build}/xxfi_db/i320/build.xml"/>	

 		<propertyregex property="x" input="${file}" regexp="(\$\{schema_password\})(\@)(\$\{oracle_sid\})" select="\1 \3" override="true"/>
 		<echo message="value of x is ${x}"/-->		

		<replaceregexp file="${db_build}/xxfi_db/i320/build.xml"
               match="(\$\{schema_password\})(\@)(\$\{oracle_sid\})"
               replace="${appspasswd}\2${dbsid}"
               byline="true"/>                             

		<!--loadfile property="file2" srcfile="${db_build}/xxfi_db/i320/build.xml"/>	               

 		<propertyregex property="x" input="${file2}" regexp="(${appspasswd})(\@)(${dbsid})" select="\1 \3" override="true"/>
 		<echo message="value of x is ${x}"/-->		               

<!-- End of preparing xxfi_db/i320/build.xml -->

		<echo message=""/>
		<echo message="===================================== ! Info ! ==========================================="/>
		<echo message=""/>



		<echo message="=========================================================================================="/>
		<echo message="=========================== Generating db_patch_binaries ================================="/>
		<echo message=""/>		

		<shellscript shell="bash">
			 	    
    	    rm db_build/*.*      
            
            chmod -R 777 db_build

            cd db_build		      

            find . -exec ${d2u} --safe {} \;

			tar -cvzf xxfi_db.tar.gz xxfi_db

        </shellscript>
        <!-- find . -exec ${d2u} dash dash safe {} \; For windows machine, d2u refers to place where docker utility is installed on the machine-->		

		<echo message=""/>
		<echo message="=========================== Generating db_patch_binaries ================================="/>
		<echo message="=========================================================================================="/>        


		<echo message=""/>
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="======================================================= prepare_db_installation ==================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>

<!-- End of targets to prepare-db-installation -->

<!-- Start of targets to install_db -->	

<!-- Start of targets to remove existign class files on the db remote -->

	<target name="db_patch_remove_existing_class_files" depends="clock">		
		<echo message="==================================================================================================================================="/>
		<echo message="=============================================== db_patch_remove_existing_class_files =============================================="/> 
		<echo message=""/>			
		<echo message="B_Start: ${clock}"/>		
		<echo message=""/>	


		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<loadproperties srcFile="${db_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>
		<propertycopy name="db_package_name" from="db.package.name"/>

		<!--loadfile property="db_patch_binary_list" srcfile="${db_build}/lst/cebdb_pack_${db_package_name}.lst"/-->
		<loadfile property="db_patch_binary_list" srcfile="${installation_logs}/${branch_name}/${package_name}/${db_package_name}/binaries/db_build/lst/cebdb_pack_${db_package_name}.lst"/>


		<foreach list="${db_patch_binary_list}" target="db_patch_remove_existing_class_files_sub_process" param="lineitem" delimiter="${line.separator}" inheritall="true"/>

		<echo message=""/>			
		<echo message="B_End: ${clock}"/>
		<echo message=""/>			
		<echo message="============================================= db_patch_remove_existing_class_files =============================================="/> 
		<echo message="==================================================================================================================================="/>
	</target>

	<target name="db_patch_remove_existing_class_files_sub_process">
		<echo message=""/>			

        <!--mkdir dir="${installation_logs}/${branch_name}/${db_package_name}/"/--> 				

 		<propertyregex property="delivery_code" input="${lineitem}" regexp="(xxfi_db_)(\w+)" select="\2" override="true"/>

 		<echo message="${delivery_code}"/>				

 		<sshexec host="${dbhost}" username="${dbuser}" password="${dbpasswd}" command="source .bash_profile run; echo &#x00022;Start:&#x00022;;date; rm -r $HOME/CEBDB_PACK/xxfi_db/${delivery_code}/build; echo &#x00022;End&#x00022;; date" trust="true" output="${installation_logs}/${branch_name}/${package_name}/${db_package_name}/${host_name}/db_patch_remove_existing_class_files.log" append="true" failonerror="false" verbose="false" timeout="60000"/>
		<echo message=""/>	 		
	</target>	

<!-- End of targets to remove existign class files on the db remote -->

		<target name="install_db" depends="clock">
		<echo message="==================================================================================================================================="/>
		<echo message="============================================================= Install_db =========================================================="/>
		<echo message=""/>
		<echo message="B_Start: ${clock}"/>
		<echo message=""/>

		<antcall target="scp_db_binary"/>
		<antcall target="db_binary_unpack"/>
		<antcall target="db_install"/>
		<antcall target="scp_db_inst_log"/>

		<echo message=""/>
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="============================================================= Install_db =========================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>
	<target name="install_db_patch" depends="clock">
		<echo message="==================================================================================================================================="/>
		<echo message="========================================================== Install_db_patch ======================================================="/>
		<echo message=""/>
		<echo message="B_Start: ${clock}"/>
		<echo message=""/>

		<antcall target="scp_db_binary"/>
		<antcall target="db_patch_remove_existing_class_files"/>
		<antcall target="db_patch_binary_unpack"/>
		<antcall target="db_patch_binary_install"/>
		<antcall target="scp_db_binary_inst_log"/>		

		<echo message=""/>
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="========================================================== Install_db_patch ======================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>	

<!-- End of targets to install-db -->

<!-- Start of targets to prod_binaries -->

	<target name="create_binaries" depends="clock">

		<echo message="==================================================================================================================================="/>
		<echo message="========================================================= create_binaries ========================================================="/>
		<echo message=""/>
		<echo message="B_Start: ${clock}"/>		
		<echo message=""/>

		<property file="${tmp}/installation_to_include.properties"/>
		<echo message="app_patch - ${app_patch}"/>
		<echo message="jar_patch - ${jar_patch}"/>		
		<echo message="db_patch - ${db_patch}"/>

		<if>
			<and>
				<equals arg1="${jar_patch}" arg2="1"/>
				<equals arg1="${app_patch}" arg2="1"/>
				<equals arg1="${db_patch}" arg2="1"/>
			</and>
				<then>

					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="=================== Binary will be created for DB, JAR and APP patch ====================="/>
					<echo message="===================================== ! Info ! ==========================================="/>

					<loadproperties srcFile="${app_build_property_file}"/>	
					<loadproperties srcFile="${db_build_property_file}"/>
					<loadproperties srcFile="${branch_to_build_property_file}"/>
					<loadproperties srcFile="${active_deploy_env_property_file}"/>		
					<propertycopy name="package_name" from="package.name"/>
					<propertycopy name="db_package_name" from="db.package.name"/>
					<propertycopy name="branch_name" from="git_branch"/>
					<propertycopy name="host_name" from="hostname"/>														

					<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp"/>
					<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${db_package_name}/${host_name}/tmp"/>												

					<antcall target="make_db_package"/>
					<antcall target="create_jar"/>
					<antcall target="make_package"/>

				</then>
		<elseif>
			<and>
				<equals arg1="${jar_patch}" arg2="1"/>
				<equals arg1="${app_patch}" arg2="1"/>
			</and>
				<then>

					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="===================== Binary will be created for JAR and APP patch ======================="/>					
					<echo message="===================================== ! Info ! ==========================================="/>

					<loadproperties srcFile="${app_build_property_file}"/>	
					<loadproperties srcFile="${branch_to_build_property_file}"/>
					<loadproperties srcFile="${active_deploy_env_property_file}"/>		
					<propertycopy name="package_name" from="package.name"/>
					<propertycopy name="branch_name" from="git_branch"/>
					<propertycopy name="host_name" from="hostname"/>

					<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp"/>							
		
					<antcall target="create_jar"/>
					<antcall target="make_package"/>

				</then>
		</elseif>
		<elseif>
				<and>
					<equals arg1="${jar_patch}" arg2="1"/>
					<equals arg1="${db_patch}" arg2="1"/>
				</and>
					<then>

						<echo message="===================================== ! Info ! ==========================================="/>
						<echo message="===================== Binary will be created for DB and JAR patch ========================"/>						
						<echo message="===================================== ! Info ! ==========================================="/>

						<loadproperties srcFile="${app_build_property_file}"/>	
						<loadproperties srcFile="${db_build_property_file}"/>
						<loadproperties srcFile="${branch_to_build_property_file}"/>
						<loadproperties srcFile="${active_deploy_env_property_file}"/>		
						<propertycopy name="package_name" from="package.name"/>
						<propertycopy name="db_package_name" from="db.package.name"/>
						<propertycopy name="branch_name" from="git_branch"/>
						<propertycopy name="host_name" from="hostname"/>

						<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp"/>
						<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${db_package_name}/${host_name}/tmp"/>							

						<antcall target="make_db_package"/>
						<antcall target="create_jar"/>

					</then>
		</elseif>
		<elseif>
				<and>
					<equals arg1="${app_patch}" arg2="1"/>
					<equals arg1="${db_patch}" arg2="1"/>
				</and>
					<then>

						<echo message="===================================== ! Info ! ==========================================="/>
						<echo message="===================== Binary will be created for DB and APP patch ========================"/>						
						<echo message="===================================== ! Info ! ==========================================="/>

						<loadproperties srcFile="${app_build_property_file}"/>	
						<loadproperties srcFile="${db_build_property_file}"/>
						<loadproperties srcFile="${branch_to_build_property_file}"/>
						<loadproperties srcFile="${active_deploy_env_property_file}"/>		
						<propertycopy name="package_name" from="package.name"/>
						<propertycopy name="db_package_name" from="db.package.name"/>
						<propertycopy name="branch_name" from="git_branch"/>
						<propertycopy name="host_name" from="hostname"/>

						<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp"/>
						<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${db_package_name}/${host_name}/tmp"/>											

						<antcall target="make_db_package"/>
						<antcall target="make_package"/>

					</then>
		</elseif>		
		<elseif>
				<equals arg1="${app_patch}" arg2="1"/>
					<then>

						<echo message="===================================== ! Info ! ==========================================="/>
						<echo message="========================= Binary will be created for APP patch ==========================="/>							
						<echo message="===================================== ! Info ! ==========================================="/>

						<loadproperties srcFile="${app_build_property_file}"/>	
						<loadproperties srcFile="${branch_to_build_property_file}"/>
						<loadproperties srcFile="${active_deploy_env_property_file}"/>		
						<propertycopy name="package_name" from="package.name"/>
						<propertycopy name="branch_name" from="git_branch"/>
						<propertycopy name="host_name" from="hostname"/>

						<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp"/>										

						<antcall target="make_package"/>						

				</then>
		</elseif>
		<elseif>
				<equals arg1="${db_patch}" arg2="1"/>
					<then>

						<echo message="===================================== ! Info ! ==========================================="/>
						<echo message="========================== Binary will be created for DB patch ==========================="/>	
						<echo message="===================================== ! Info ! ==========================================="/>

						<loadproperties srcFile="${app_build_property_file}"/>	
						<loadproperties srcFile="${db_build_property_file}"/>
						<loadproperties srcFile="${branch_to_build_property_file}"/>
						<loadproperties srcFile="${active_deploy_env_property_file}"/>		
						<propertycopy name="package_name" from="package.name"/>
						<propertycopy name="db_package_name" from="db.package.name"/>
						<propertycopy name="branch_name" from="git_branch"/>
						<propertycopy name="host_name" from="hostname"/>

						<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp"/>
						<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${db_package_name}/${host_name}/tmp"/>							

						<antcall target="make_db_package"/>

				</then>
		</elseif>
		<elseif>
				<equals arg1="${jar_patch}" arg2="1"/>
					<then>

						<echo message="===================================== ! Info ! ==========================================="/>
						<echo message="========================= Binary will be created for JAR patch ==========================="/>
						<echo message="===================================== ! Info ! ==========================================="/>

						<loadproperties srcFile="${app_build_property_file}"/>	
						<loadproperties srcFile="${branch_to_build_property_file}"/>
						<loadproperties srcFile="${active_deploy_env_property_file}"/>		
						<propertycopy name="package_name" from="package.name"/>
						<propertycopy name="branch_name" from="git_branch"/>
						<propertycopy name="host_name" from="hostname"/>

						<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp"/>								

						<antcall target="create_jar"/>

				</then>
		</elseif>								
		</if>

		<!-- start of compress binaries to be uploaded artifactory -->
		<!--loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/-->				

		<antcall target="tar">			
			<param name="dir_to_compress" value="${installation_logs}/${branch_name}/${package_name}"/>			
			<param name="destination" value="${installation_logs}/${branch_name}"/>
		</antcall>
		<!-- end of compress binaries to be uploaded artifactory -->

		<!-- start of upload binaries to artifactory -->
		<loadproperties srcFile="${build_property_file}"/>
		<propertycopy name="build_target" from="build_target"/>

		<if>
			<equals arg1="${build_target_temp_var}" arg2="p" casesensitive="No"/>
				<then>
					<propertycopy name="artifactory_host" from="artifactory.prod.host"/>
					<propertycopy name="artifactory_uid" from="artifactory.username"/>
					<propertycopy name="artifactory_pwd" from="artifactory.password"/>
				</then>
			<else>
				<propertycopy name="artifactory_host" from="artifactory.test.host"/>
				<propertycopy name="artifactory_uid" from="artifactory.username"/>
				<propertycopy name="artifactory_pwd" from="artifactory.password"/>				
			</else>
		</if>

		<antcall target="upload_to_artifactory">			
			<param name="binary_file" value="${installation_logs}/${branch_name}/${package_name}.tar.gz"/>
			<param name="destination" value="${artifactory_host}"/>
			<param name="uid" value="${artifactory_uid}"/>
			<param name="pwd" value="${artifactory_pwd}"/>						
		</antcall>

		<antcall target="checkfile_at_artificatory">			
			<param name="binary_file" value="${package_name}.tar.gz"/>
			<param name="destination" value="${artifactory_host}"/>
			<param name="uid" value="${artifactory_uid}"/>
			<param name="pwd" value="${artifactory_pwd}"/>						
		</antcall>

		<loadfile property="file" srcfile="${transit_point}/checkfile_at_artifactory_output.log"/>
		<propertyregex property="status_code" input="${file}" regexp="(HTTP\/1.1.)(\d+)" select="\2" override="true"/>
		<echo message="status_code: ${status_code}"/>

		<if>
			<equals arg1="${status_code}" arg2="200"/>
				<then>
					<echo message=""/>
					<shellscript shell="bash">
						touch "${artifactory_file_status_property_file}"
						./utility/setprop.sh "file_available_at_artifactory" "0" "${artifactory_file_status_property_file}"
					</shellscript>						
				</then>
			<else>
				<echo message="===================================== ! Alert ! =========================================="/>			
				<echo message="============ Error reading file availability. Require manual intervention ================"/>
				<echo message="===================================== ! Alert ! =========================================="/>	
				<shellscript shell="bash">
					touch "${artifactory_file_status_property_file}"
					./utility/setprop.sh "file_available_at_artifactory" "E" "${artifactory_file_status_property_file}"
				</shellscript>										
			</else>
		</if>
		<!-- end of upload binaries to artifactory -->													

		<!--antcall target="create_rollback_binaries"/-->	

		<echo message=""/>				
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="========================================================= create_binaries ========================================================="/>					
		<echo message="==================================================================================================================================="/>		
	</target>

	<target name="create_rollback_binaries" depends="clock">

		<echo message="==================================================================================================================================="/>
		<echo message="===================================================== create_rollback_binaries ===================================================="/>					
		<echo message=""/>
		<echo message="B_Start: ${clock}"/>		
		<echo message=""/> 	

		<antcall target="init_build_workspace"/>

		<antcall target="rollback_build_checkout"/>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>	
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>

		<property file="${tmp}/installation_to_include.properties"/>	
		
		<!--echo message="app_patch - ${app_patch}"/>
		<echo message="jar_patch - ${jar_patch}"/>		
		<echo message="db_patch - ${db_patch}"/-->

		<if>
			<and>
				<equals arg1="${jar_patch}" arg2="1"/>
				<equals arg1="${app_patch}" arg2="1"/>
				<equals arg1="${db_patch}" arg2="1"/>
			</and>
				<then>

					<loadproperties srcFile="${db_build_property_file}"/>	
					<loadproperties srcFile="${app_build_property_file}"/>	       					

					<propertycopy name="package_name" from="package.name"/>
					<propertycopy name="db_package_name" from="db.package.name"/>						

					<shellscript shell="bash">
              
						package=`cat ./tmp/temp.properties | grep package.name | cut -d '=' -f 2`
						dbpackage=`cat ./tmp/temp_db.properties | grep db.package.name | cut -d '=' -f 2`
						branch=`cat ./tmp/branch.properties | grep git_branch | cut -d '=' -f 2` 

            			mv tmp/ceb_pack_${package}.lst tmp/ceb_pack_${package}-RB.lst
            			mv tmp/cebdb_pack_${dbpackage}.lst tmp/cebdb_pack_${dbpackage}-RB.lst 

						./utility/setprop.sh "package.name" "${package}-RB" "${app_build_property_file}"
						./utility/setprop.sh "db.package.name" "${dbpackage}-RB" "${db_build_property_file}"   					

        			</shellscript>	

				</then>
		<elseif>
			<and>
				<equals arg1="${jar_patch}" arg2="1"/>
				<equals arg1="${app_patch}" arg2="1"/>
			</and>
				<then>

					<loadproperties srcFile="${app_build_property_file}"/>	       					

					<propertycopy name="package_name" from="package.name"/>

					<shellscript shell="bash">
              
						package=`cat ./tmp/temp.properties | grep package.name | cut -d '=' -f 2`
						branch=`cat ./tmp/branch.properties | grep git_branch | cut -d '=' -f 2` 

            			mv tmp/ceb_pack_${package}.lst tmp/ceb_pack_${package}-RB.lst

						./utility/setprop.sh "package.name" "${package}-RB" "${app_build_property_file}"

        			</shellscript>	

				</then>
		</elseif>
		<elseif>
				<and>
					<equals arg1="${jar_patch}" arg2="1"/>
					<equals arg1="${db_patch}" arg2="1"/>
				</and>
					<then>

						<loadproperties srcFile="${db_build_property_file}"/>	

						<propertycopy name="db_package_name" from="db.package.name"/>						

						<shellscript shell="bash">
              				
              				package=`cat ./tmp/temp.properties | grep package.name | cut -d '=' -f 2`	
							dbpackage=`cat ./tmp/temp_db.properties | grep db.package.name | cut -d '=' -f 2`
							branch=`cat ./tmp/branch.properties | grep git_branch | cut -d '=' -f 2` 

            				mv tmp/cebdb_pack_${dbpackage}.lst tmp/cebdb_pack_${dbpackage}-RB.lst 

            				./utility/setprop.sh "package.name" "${package}-RB" "${app_build_property_file}"
							./utility/setprop.sh "db.package.name" "${dbpackage}-RB" "${db_build_property_file}"	

        				</shellscript>	

					</then>
		</elseif>
		<elseif>
				<and>
					<equals arg1="${app_patch}" arg2="1"/>
					<equals arg1="${db_patch}" arg2="1"/>
				</and>
					<then>

						<loadproperties srcFile="${db_build_property_file}"/>	
						<loadproperties srcFile="${app_build_property_file}"/>	       					

						<propertycopy name="package_name" from="package.name"/>
						<propertycopy name="db_package_name" from="db.package.name"/>						

						<shellscript shell="bash">
              
							package=`cat ./tmp/temp.properties | grep package.name | cut -d '=' -f 2`
							dbpackage=`cat ./tmp/temp_db.properties | grep db.package.name | cut -d '=' -f 2`
							branch=`cat ./tmp/branch.properties | grep git_branch | cut -d '=' -f 2` 

            				mv tmp/ceb_pack_${package}.lst tmp/ceb_pack_${package}-RB.lst
            				mv tmp/cebdb_pack_${dbpackage}.lst tmp/cebdb_pack_${dbpackage}-RB.lst 

							./utility/setprop.sh "package.name" "${package}-RB" "${app_build_property_file}"
							./utility/setprop.sh "db.package.name" "${dbpackage}-RB" "${db_build_property_file}"   					

        				</shellscript>	

					</then>
		</elseif>		
		<elseif>
				<equals arg1="${app_patch}" arg2="1"/>
					<then>

						<loadproperties srcFile="${app_build_property_file}"/>	       					

						<propertycopy name="package_name" from="package.name"/>						

						<shellscript shell="bash">
              
							package=`cat ./tmp/temp.properties | grep package.name | cut -d '=' -f 2`
							branch=`cat ./tmp/branch.properties | grep git_branch | cut -d '=' -f 2` 

            				mv tmp/ceb_pack_${package}.lst tmp/ceb_pack_${package}-RB.lst

							./utility/setprop.sh "package.name" "${package}-RB" "${app_build_property_file}"

        				</shellscript>							

				</then>
		</elseif>
		<elseif>
				<equals arg1="${db_patch}" arg2="1"/>
					<then>

						<loadproperties srcFile="${db_build_property_file}"/>	

						<propertycopy name="db_package_name" from="db.package.name"/>							

						<shellscript shell="bash">
              				
              				package=`cat ./tmp/temp.properties | grep package.name | cut -d '=' -f 2`
							dbpackage=`cat ./tmp/temp_db.properties | grep db.package.name | cut -d '=' -f 2`
							branch=`cat ./tmp/branch.properties | grep git_branch | cut -d '=' -f 2` 

            				mv tmp/cebdb_pack_${dbpackage}.lst tmp/cebdb_pack_${dbpackage}-RB.lst 

            				./utility/setprop.sh "package.name" "${package}-RB" "${app_build_property_file}"
							./utility/setprop.sh "db.package.name" "${dbpackage}-RB" "${db_build_property_file}"   					

        				</shellscript>	

				</then>
		</elseif>
		<elseif>
				<equals arg1="${jar_patch}" arg2="1"/>
					<then>
						<echo message="=================== ! No tmp property setting applicable ! ==============================="/>
				</then>
		</elseif>								
		</if>

		<var name="branch_name" unset="true"/>

		<unset file="${branch_to_build_property_file}"/>
		<unset file="${active_deploy_env_property_file}"/>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>

		<property file="${tmp}/installation_to_include.properties"/>	
		
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>

		<echo message="app_patch - ${app_patch}"/>
		<echo message="jar_patch - ${jar_patch}"/>		
		<echo message="db_patch - ${db_patch}"/>

		<if>
			<and>
				<equals arg1="${jar_patch}" arg2="1"/>
				<equals arg1="${app_patch}" arg2="1"/>
				<equals arg1="${db_patch}" arg2="1"/>
			</and>
				<then>

					<var name="package_name" unset="true"/>
					<var name="db_package_name" unset="true"/>	       

					<unset file="${db_build_property_file}"/>	
					<unset file="${app_build_property_file}"/>	

					<loadproperties srcFile="${db_build_property_file}"/>	
					<loadproperties srcFile="${app_build_property_file}"/>	       
		
					<propertycopy name="package_name" from="package.name"/>
					<propertycopy name="db_package_name" from="db.package.name"/>	

    				<copy includeemptydirs="false" todir="${installation_logs}/${branch_name}/${package_name}/tmp/">
			            <fileset dir="${tmp}"/>
			    	</copy> 

    				<copy includeemptydirs="false" todir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp/">
			            <fileset dir="${tmp}"/>
			    	</copy> 		

    				<copy includeemptydirs="false" todir="${installation_logs}/${branch_name}/${package_name}/${db_package_name}/${host_name}/tmp/">
			            <fileset dir="${tmp}"/>
			    	</copy>   					

					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="=================== Binary will be created for DB, JAR and APP patch ====================="/>
					<echo message="===================================== ! Info ! ==========================================="/>					
					
					<antcall target="make_db_package"/>
					<antcall target="create_jar"/>
					<antcall target="make_package"/>

				</then>
		<elseif>
			<and>
				<equals arg1="${jar_patch}" arg2="1"/>
				<equals arg1="${app_patch}" arg2="1"/>
			</and>
				<then>

					<var name="package_name" unset="true"/>

					<unset file="${app_build_property_file}"/>	

					<loadproperties srcFile="${app_build_property_file}"/>	       
		
					<propertycopy name="package_name" from="package.name"/>					

    				<copy includeemptydirs="false" todir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp/">
			            <fileset dir="${tmp}"/>
			    	</copy> 		

					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="===================== Binary will be created for JAR and APP patch ======================="/>					
					<echo message="===================================== ! Info ! ==========================================="/>
		
					<antcall target="create_jar"/>
					<antcall target="make_package"/>

				</then>
		</elseif>
		<elseif>
				<and>
					<equals arg1="${jar_patch}" arg2="1"/>
					<equals arg1="${db_patch}" arg2="1"/>
				</and>
					<then>

						<var name="package_name" unset="true"/>
						<var name="db_package_name" unset="true"/>	       

						<unset file="${app_build_property_file}"/>
						<unset file="${db_build_property_file}"/>	

						<loadproperties srcFile="${app_build_property_file}"/>
						<loadproperties srcFile="${db_build_property_file}"/>	   
		
						<propertycopy name="package_name" from="package.name"/>
						<propertycopy name="db_package_name" from="db.package.name"/>						

	    				<copy includeemptydirs="false" todir="${installation_logs}/${branch_name}/${package_name}/tmp/">
				            <fileset dir="${tmp}"/>
				    	</copy> 

	    				<copy includeemptydirs="false" todir="${installation_logs}/${branch_name}/${package_name}/${db_package_name}/${host_name}/tmp/">
				            <fileset dir="${tmp}"/>
				    	</copy>  

						<echo message="===================================== ! Info ! ==========================================="/>
						<echo message="===================== Binary will be created for DB and JAR patch ========================"/>						
						<echo message="===================================== ! Info ! ==========================================="/>

						<antcall target="make_db_package"/>
						<antcall target="create_jar"/>

					</then>
		</elseif>
		<elseif>
				<and>
					<equals arg1="${app_patch}" arg2="1"/>
					<equals arg1="${db_patch}" arg2="1"/>
				</and>
					<then>

						<var name="package_name" unset="true"/>
						<var name="db_package_name" unset="true"/>	       

						<unset file="${db_build_property_file}"/>	
						<unset file="${app_build_property_file}"/>	

						<loadproperties srcFile="${db_build_property_file}"/>	
						<loadproperties srcFile="${app_build_property_file}"/>	       
		
						<propertycopy name="package_name" from="package.name"/>
						<propertycopy name="db_package_name" from="db.package.name"/>	


    					<copy includeemptydirs="false" todir="${installation_logs}/${branch_name}/${package_name}/tmp/">
			        	    <fileset dir="${tmp}"/>
			    		</copy> 

    					<copy includeemptydirs="false" todir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp/">
			        	    <fileset dir="${tmp}"/>
			    		</copy> 		

    					<copy includeemptydirs="false" todir="${installation_logs}/${branch_name}/${package_name}/${db_package_name}/${host_name}/tmp/">
			            	<fileset dir="${tmp}"/>
			    		</copy>  						

						<echo message="===================================== ! Info ! ==========================================="/>
						<echo message="===================== Binary will be created for DB and APP patch ========================"/>						
						<echo message="===================================== ! Info ! ==========================================="/>				

						<antcall target="make_db_package"/>
						<antcall target="make_package"/>

					</then>
		</elseif>		
		<elseif>
				<equals arg1="${app_patch}" arg2="1"/>
					<then>

						<var name="package_name" unset="true"/>

						<unset file="${app_build_property_file}"/>	

						<loadproperties srcFile="${app_build_property_file}"/>	       
		
						<propertycopy name="package_name" from="package.name"/>


	    				<copy includeemptydirs="false" todir="${installation_logs}/${branch_name}/${package_name}/tmp/">
				            <fileset dir="${tmp}"/>
				    	</copy>
	    				<copy includeemptydirs="false" todir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp/">
				            <fileset dir="${tmp}"/>
				    	</copy> 		

						<echo message="===================================== ! Info ! ==========================================="/>
						<echo message="========================= Binary will be created for APP patch ==========================="/>							
						<echo message="===================================== ! Info ! ==========================================="/>				

						<antcall target="make_package"/>						

				</then>
		</elseif>
		<elseif>
				<equals arg1="${db_patch}" arg2="1"/>
					<then>

						<var name="package_name" unset="true"/>
						<var name="db_package_name" unset="true"/>	       

						<unset file="${app_build_property_file}"/>
						<unset file="${db_build_property_file}"/>	

						<loadproperties srcFile="${app_build_property_file}"/>
						<loadproperties srcFile="${db_build_property_file}"/>	   
		
						<propertycopy name="package_name" from="package.name"/>							
						<propertycopy name="db_package_name" from="db.package.name"/>							


	    				<copy includeemptydirs="false" todir="${installation_logs}/${branch_name}/${package_name}/tmp/">
				            <fileset dir="${tmp}"/>
				    	</copy>

	    				<copy includeemptydirs="false" todir="${installation_logs}/${branch_name}/${package_name}/${db_package_name}/${host_name}/tmp/">
				            <fileset dir="${tmp}"/>
				    	</copy>  						

						<echo message="===================================== ! Info ! ==========================================="/>
						<echo message="========================== Binary will be created for DB patch ==========================="/>	
						<echo message="===================================== ! Info ! ==========================================="/>

						<antcall target="make_db_package"/>

				</then>
		</elseif>
		<elseif>
				<equals arg1="${jar_patch}" arg2="1"/>
					<then>
						<var name="package_name" unset="true"/>

						<unset file="${app_build_property_file}"/>	

						<loadproperties srcFile="${app_build_property_file}"/>	       
		
						<propertycopy name="package_name" from="package.name"/>


	    				<copy includeemptydirs="false" todir="${installation_logs}/${branch_name}/${package_name}/tmp/">
				            <fileset dir="${tmp}"/>
				    	</copy>
	    				<copy includeemptydirs="false" todir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp/">
				            <fileset dir="${tmp}"/>
				    	</copy> 

						<echo message="===================================== ! Info ! ==========================================="/>
						<echo message="========================= Binary will be created for JAR patch ==========================="/>
						<echo message="===================================== ! Info ! ==========================================="/>

					<antcall target="create_jar"/>

				</then>
		</elseif>								
		</if>

		<!-- start of compress binaries to be uploaded artifactory -->
		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>			

		<antcall target="tar">			
			<param name="dir_to_compress" value="${installation_logs}/${branch_name}/${package_name}"/>			
			<param name="destination" value="${installation_logs}/${branch_name}"/>
		</antcall>
		<!-- end of compress binaries to be uploaded artifactory -->

		<!-- start of upload binaries to artifactory -->
		<loadproperties srcFile="${build_property_file}"/>
		<propertycopy name="build_target" from="build_target"/>

		<if>
			<equals arg1="${build_target_temp_var}" arg2="p" casesensitive="No"/>
				<then>
					<propertycopy name="artifactory_host" from="artifactory.prod.host"/>
					<propertycopy name="artifactory_uid" from="artifactory.username"/>
					<propertycopy name="artifactory_pwd" from="artifactory.password"/>
				</then>
			<else>
				<propertycopy name="artifactory_host" from="artifactory.test.host"/>
				<propertycopy name="artifactory_uid" from="artifactory.username"/>
				<propertycopy name="artifactory_pwd" from="artifactory.password"/>				
			</else>
		</if>

		<antcall target="upload_to_artifactory">			
			<param name="binary_file" value="${installation_logs}/${branch_name}/${package_name}.tar.gz"/>
			<param name="destination" value="${artifactory_host}"/>
			<param name="uid" value="${artifactory_uid}"/>
			<param name="pwd" value="${artifactory_pwd}"/>						
		</antcall>

		<antcall target="checkfile_at_artificatory">			
			<param name="binary_file" value="${package_name}.tar.gz"/>
			<param name="destination" value="${artifactory_host}"/>
			<param name="uid" value="${artifactory_uid}"/>
			<param name="pwd" value="${artifactory_pwd}"/>						
		</antcall>

		<loadfile property="file" srcfile="${transit_point}/checkfile_at_artifactory_output.log"/>
		<propertyregex property="status_code" input="${file}" regexp="(HTTP\/1.1.)(\d+)" select="\2" override="true"/>
		<echo message="status_code: ${status_code}"/>

		<if>
			<equals arg1="${status_code}" arg2="200"/>
				<then>
					<echo message=""/>
					<shellscript shell="bash">
						touch "${artifactory_file_status_property_file}"
						./utility/setprop.sh "file_available_at_artifactory" "0" "${artifactory_file_status_property_file}"
					</shellscript>						
				</then>
			<else>
				<echo message="===================================== ! Alert ! =========================================="/>			
				<echo message="============ Error reading file availability. Require manual intervention ================"/>
				<echo message="===================================== ! Alert ! =========================================="/>	
				<shellscript shell="bash">
					touch "${artifactory_file_status_property_file}"
					./utility/setprop.sh "file_available_at_artifactory" "E" "${artifactory_file_status_property_file}"
				</shellscript>										
			</else>
		</if>
		<!-- end of upload binaries to artifactory -->			

		<echo message=""/>				
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="===================================================== create_rollback_binaries ===================================================="/>					
		<echo message="==================================================================================================================================="/>		
	</target>


<!-- End of targets to prod_binaries -->	

<!-- Start of targets to auto-deploy -->
<target name="deploy+" depends="clock">

		<echo message="==================================================================================================================================="/>
		<echo message="============================================================ auto_deploy+ ========================================================="/>					
		<echo message=""/>
		<echo message="B_Start: ${clock}"/>		
		<echo message=""/>

		<antcall target="installation_summary"/>

		<shellscript shell="bash">
			cp tmp/installation_status.properties tmp/installation_status_bkup.properties
        </shellscript>

		<property file="${tmp}/installation_to_include.properties"/>
		<echo message="app_patch - ${app_patch}"/>
		<echo message="jar_patch - ${jar_patch}"/>		
		<echo message="db_patch - ${db_patch}"/>

		<if>
			<and>
				<equals arg1="${jar_patch}" arg2="1"/>
				<equals arg1="${app_patch}" arg2="1"/>
				<equals arg1="${db_patch}" arg2="1"/>
			</and>
				<then>

					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="==================== Deploy+ will be done for DB, JAR and APP patch ======================"/>
					<echo message="===================================== ! Info ! ==========================================="/>

					<loadproperties srcFile="${app_build_property_file}"/>	
					<loadproperties srcFile="${db_build_property_file}"/>
					<loadproperties srcFile="${branch_to_build_property_file}"/>
					<loadproperties srcFile="${active_deploy_env_property_file}"/>		
					<propertycopy name="package_name" from="package.name"/>
					<propertycopy name="db_package_name" from="db.package.name"/>
					<propertycopy name="branch_name" from="git_branch"/>
					<propertycopy name="host_name" from="hostname"/>														

					<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp"/>
					<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${db_package_name}/${host_name}/tmp"/>					
					
					<antcall target="make_db_package"/>
					<antcall target="install_db_patch"/>						
					<antcall target="create_jar"/>
					<antcall target="JAR_File_Upload"/>					
					<antcall target="make_package"/>
					<antcall target="scp_app_binary"/>					
					<antcall target="app_binary_unpack"/>
					<antcall target="app_binary_install"/>

					<if>
						<equals arg1="${MD120_JSP_Installation_xxfi_i673}" arg2="1"/>
							<then>
								<antcall target="MD120_i673_install"/>
							</then>
						<else>
							<echo message="===================================== ! Info ! ==========================================="/>
							<echo message="=================== Not Applicable - MD120_i673_install - Not Applicable ================="/>
							<echo message="===================================== ! Info ! ==========================================="/>	
						</else>				
					</if>

					<if>
						<equals arg1="${MD120_JSP_Installation_xxfe_f694_f695}" arg2="1"/>
							<then>
								<antcall target="MD120_f694_f695_install"/>
							</then>
						<else>
							<echo message="===================================== ! Info ! ==========================================="/>
							<echo message="================ Not Applicable - MD120_f694_f695_install - Not Applicable ==============="/>
							<echo message="===================================== ! Info ! ==========================================="/>	
						</else>				
					</if>	

					<antcall target="tomcat_restart"/>						
					<antcall target="middle_tier_restart"/>					
					<antcall target="cm_restart"/>																			

				</then>
		<elseif>
			<and>
				<equals arg1="${jar_patch}" arg2="1"/>
				<equals arg1="${app_patch}" arg2="1"/>
			</and>
				<then>

					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="======================= Deploy+ will be done for JAR and APP patch ======================="/>
					<echo message="===================================== ! Info ! ==========================================="/>

					<loadproperties srcFile="${app_build_property_file}"/>	
					<loadproperties srcFile="${branch_to_build_property_file}"/>
					<loadproperties srcFile="${active_deploy_env_property_file}"/>		
					<propertycopy name="package_name" from="package.name"/>
					<propertycopy name="branch_name" from="git_branch"/>
					<propertycopy name="host_name" from="hostname"/>

					<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp"/>												
					
					<antcall target="create_jar"/>
					<antcall target="JAR_File_Upload"/>
					<antcall target="make_package"/>
					<antcall target="scp_app_binary"/>
					<antcall target="app_binary_unpack"/>
					<antcall target="app_binary_install"/>

					<if>
						<equals arg1="${MD120_JSP_Installation_xxfi_i673}" arg2="1"/>
							<then>
								<antcall target="MD120_i673_install"/>
							</then>
						<else>
							<echo message="===================================== ! Info ! ==========================================="/>
							<echo message="=================== Not Applicable - MD120_i673_install - Not Applicable ================="/>
							<echo message="===================================== ! Info ! ==========================================="/>	
						</else>				
					</if>

					<if>
						<equals arg1="${MD120_JSP_Installation_xxfe_f694_f695}" arg2="1"/>
							<then>
								<antcall target="MD120_f694_f695_install"/>
							</then>
						<else>
							<echo message="===================================== ! Info ! ==========================================="/>
							<echo message="================ Not Applicable - MD120_f694_f695_install - Not Applicable ==============="/>
							<echo message="===================================== ! Info ! ==========================================="/>	
						</else>				
					</if>	

					<antcall target="tomcat_restart"/>						
					<antcall target="middle_tier_restart"/>					
					<antcall target="cm_restart"/>									

				</then>
		</elseif>
		<elseif>
				<and>
					<equals arg1="${jar_patch}" arg2="1"/>
					<equals arg1="${db_patch}" arg2="1"/>
				</and>
					<then>

						<echo message="===================================== ! Info ! ==========================================="/>
						<echo message="======================= Deploy+ will be done for JAR and DB patch ========================"/>
						<echo message="===================================== ! Info ! ==========================================="/>

						<loadproperties srcFile="${app_build_property_file}"/>	
						<loadproperties srcFile="${db_build_property_file}"/>
						<loadproperties srcFile="${branch_to_build_property_file}"/>
						<loadproperties srcFile="${active_deploy_env_property_file}"/>		
						<propertycopy name="package_name" from="package.name"/>
						<propertycopy name="db_package_name" from="db.package.name"/>
						<propertycopy name="branch_name" from="git_branch"/>
						<propertycopy name="host_name" from="hostname"/>

						<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp"/>
						<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${db_package_name}/${host_name}/tmp"/>														

						<antcall target="make_db_package"/>
						<antcall target="install_db_patch"/>						
						<antcall target="create_jar"/>
						<antcall target="JAR_File_Upload"/>

						<antcall target="tomcat_restart"/>						
						<antcall target="middle_tier_restart"/>					
						<antcall target="cm_restart"/>														

					</then>
		</elseif>
		<elseif>
				<and>
					<equals arg1="${app_patch}" arg2="1"/>
					<equals arg1="${db_patch}" arg2="1"/>
				</and>
					<then>

						<echo message="===================================== ! Info ! ==========================================="/>
						<echo message="======================= Deploy+ will be done for DB and APP patch ========================"/>
						<echo message="===================================== ! Info ! ==========================================="/>

						<loadproperties srcFile="${app_build_property_file}"/>	
						<loadproperties srcFile="${db_build_property_file}"/>
						<loadproperties srcFile="${branch_to_build_property_file}"/>
						<loadproperties srcFile="${active_deploy_env_property_file}"/>		
						<propertycopy name="package_name" from="package.name"/>
						<propertycopy name="db_package_name" from="db.package.name"/>
						<propertycopy name="branch_name" from="git_branch"/>
						<propertycopy name="host_name" from="hostname"/>

						<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp"/>
						<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${db_package_name}/${host_name}/tmp"/>													
					
						<antcall target="make_db_package"/>
						<antcall target="install_db_patch"/>											
						<antcall target="make_package"/>
						<antcall target="scp_app_binary"/>						
						<antcall target="app_binary_unpack"/>
						<antcall target="app_binary_install"/>

						<if>
							<equals arg1="${MD120_JSP_Installation_xxfi_i673}" arg2="1"/>
								<then>
									<antcall target="MD120_i673_install"/>
								</then>
							<else>
								<echo message="===================================== ! Info ! ==========================================="/>
								<echo message="=================== Not Applicable - MD120_i673_install - Not Applicable ================="/>
								<echo message="===================================== ! Info ! ==========================================="/>	
							</else>				
						</if>

						<if>
							<equals arg1="${MD120_JSP_Installation_xxfe_f694_f695}" arg2="1"/>
								<then>
									<antcall target="MD120_f694_f695_install"/>
								</then>
							<else>
								<echo message="===================================== ! Info ! ==========================================="/>
								<echo message="================ Not Applicable - MD120_f694_f695_install - Not Applicable ==============="/>
								<echo message="===================================== ! Info ! ==========================================="/>	
							</else>				
						</if>	

						<antcall target="tomcat_restart"/>						
						<antcall target="middle_tier_restart"/>					
						<antcall target="cm_restart"/>	

					</then>
		</elseif>		
		<elseif>
				<equals arg1="${app_patch}" arg2="1"/>
					<then>

						<echo message="===================================== ! Info ! ==========================================="/>
						<echo message="=========================== Deploy+ will be done for APP patch ==========================="/>
						<echo message="===================================== ! Info ! ==========================================="/>

						<loadproperties srcFile="${app_build_property_file}"/>	
						<loadproperties srcFile="${branch_to_build_property_file}"/>
						<loadproperties srcFile="${active_deploy_env_property_file}"/>		
						<propertycopy name="package_name" from="package.name"/>
						<propertycopy name="branch_name" from="git_branch"/>
						<propertycopy name="host_name" from="hostname"/>

						<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp"/>

						<antcall target="make_package"/>						
						<antcall target="scp_app_binary"/>
						<antcall target="app_binary_unpack"/>
						<antcall target="app_binary_install"/>

						<if>
							<equals arg1="${MD120_JSP_Installation_xxfi_i673}" arg2="1"/>
								<then>
									<antcall target="MD120_i673_install"/>
								</then>
							<else>
								<echo message="===================================== ! Info ! ==========================================="/>
								<echo message="=================== Not Applicable - MD120_i673_install - Not Applicable ================="/>
								<echo message="===================================== ! Info ! ==========================================="/>	
							</else>				
						</if>

						<if>
							<equals arg1="${MD120_JSP_Installation_xxfe_f694_f695}" arg2="1"/>
								<then>
									<antcall target="MD120_f694_f695_install"/>
								</then>
							<else>
								<echo message="===================================== ! Info ! ==========================================="/>
								<echo message="================ Not Applicable - MD120_f694_f695_install - Not Applicable ==============="/>
								<echo message="===================================== ! Info ! ==========================================="/>	
							</else>				
						</if>	

					<antcall target="tomcat_restart"/>	
					<antcall target="middle_tier_restart"/>					
					<antcall target="cm_restart"/>	

				</then>
		</elseif>
		<elseif>
				<equals arg1="${db_patch}" arg2="1"/>
					<then>

						<echo message="===================================== ! Info ! ==========================================="/>
						<echo message="=========================== Deploy+ will be done for DB patch ============================"/>
						<echo message="===================================== ! Info ! ==========================================="/>

						<loadproperties srcFile="${app_build_property_file}"/>	
						<loadproperties srcFile="${db_build_property_file}"/>
						<loadproperties srcFile="${branch_to_build_property_file}"/>
						<loadproperties srcFile="${active_deploy_env_property_file}"/>		
						<propertycopy name="package_name" from="package.name"/>
						<propertycopy name="db_package_name" from="db.package.name"/>
						<propertycopy name="branch_name" from="git_branch"/>
						<propertycopy name="host_name" from="hostname"/>

						<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp"/>
						<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${db_package_name}/${host_name}/tmp"/>															
					
						<antcall target="make_db_package"/>
						<antcall target="install_db_patch"/>

						<antcall target="tomcat_restart"/>						
						<antcall target="middle_tier_restart"/>					
						<antcall target="cm_restart"/>													

					</then>
		</elseif>
		<elseif>
				<equals arg1="${jar_patch}" arg2="1"/>
					<then>

						<echo message="===================================== ! Info ! ==========================================="/>
						<echo message="========================== Deploy+ will be done for JAR patch ============================"/>
						<echo message="===================================== ! Info ! ==========================================="/>

						<loadproperties srcFile="${app_build_property_file}"/>	
						<loadproperties srcFile="${branch_to_build_property_file}"/>
						<loadproperties srcFile="${active_deploy_env_property_file}"/>		
						<propertycopy name="package_name" from="package.name"/>
						<propertycopy name="branch_name" from="git_branch"/>
						<propertycopy name="host_name" from="hostname"/>

						<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp"/>				
					
						<antcall target="create_jar"/>
						<antcall target="JAR_File_Upload"/>

						<antcall target="tomcat_restart"/>						
						<antcall target="middle_tier_restart"/>					
						<antcall target="cm_restart"/>													

					</then>
		</elseif>								
		</if>

		<!-- start of compress binaries to be uploaded artifactory -->
		<!--loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/-->			

		<antcall target="tar">			
			<param name="dir_to_compress" value="${installation_logs}/${branch_name}/${package_name}"/>			
			<param name="destination" value="${installation_logs}/${branch_name}"/>
		</antcall>
		<!-- end of compress binaries to be uploaded artifactory -->

		<!-- start of upload binaries to artifactory -->
		<loadproperties srcFile="${build_property_file}"/>
		<propertycopy name="build_target" from="build_target"/>

		<if>
			<equals arg1="${build_target_temp_var}" arg2="p" casesensitive="No"/>
				<then>
					<propertycopy name="artifactory_host" from="artifactory.prod.host"/>
					<propertycopy name="artifactory_uid" from="artifactory.username"/>
					<propertycopy name="artifactory_pwd" from="artifactory.password"/>
				</then>
			<else>
				<propertycopy name="artifactory_host" from="artifactory.test.host"/>
				<propertycopy name="artifactory_uid" from="artifactory.username"/>
				<propertycopy name="artifactory_pwd" from="artifactory.password"/>				
			</else>
		</if>

		<antcall target="upload_to_artifactory">			
			<param name="binary_file" value="${installation_logs}/${branch_name}/${package_name}.tar.gz"/>
			<param name="destination" value="${artifactory_host}"/>
			<param name="uid" value="${artifactory_uid}"/>
			<param name="pwd" value="${artifactory_pwd}"/>						
		</antcall>

		<antcall target="checkfile_at_artificatory">			
			<param name="binary_file" value="${package_name}.tar.gz"/>
			<param name="destination" value="${artifactory_host}"/>
			<param name="uid" value="${artifactory_uid}"/>
			<param name="pwd" value="${artifactory_pwd}"/>						
		</antcall>

		<loadfile property="file" srcfile="${transit_point}/checkfile_at_artifactory_output.log"/>
		<propertyregex property="status_code" input="${file}" regexp="(HTTP\/1.1.)(\d+)" select="\2" override="true"/>
		<echo message="status_code: ${status_code}"/>

		<if>
			<equals arg1="${status_code}" arg2="200"/>
				<then>
					<echo message=""/>
					<shellscript shell="bash">
						touch "${artifactory_file_status_property_file}"
						./utility/setprop.sh "file_available_at_artifactory" "0" "${artifactory_file_status_property_file}"
					</shellscript>						
				</then>
			<else>
				<echo message="===================================== ! Alert ! =========================================="/>			
				<echo message="============ Error reading file availability. Require manual intervention ================"/>
				<echo message="===================================== ! Alert ! =========================================="/>	
				<shellscript shell="bash">
					touch "${artifactory_file_status_property_file}"
					./utility/setprop.sh "file_available_at_artifactory" "E" "${artifactory_file_status_property_file}"
				</shellscript>										
			</else>
		</if>
		<!-- end of upload binaries to artifactory -->											

		<!-- start of presentation of intallation summary -->
		<antcall target="installation_summary"/>
		<!-- end of presentation of intallation summary -->		

		<echo message=""/>				
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="============================================================ auto_deploy+ ========================================================="/>
		<echo message="==================================================================================================================================="/>		
	</target>



	<target name="deploy_to_env" depends="clock">

		<echo message="==================================================================================================================================="/>
		<echo message="============================================================ deploy_to_env ========================================================"/>					
		<echo message=""/>
		<echo message="B_Start: ${clock}"/>		
		<echo message=""/>

		<antcall target="reset_installation_status"/>		
		<antcall target="installation_summary"/>		

		<property file="${tmp}/installation_to_include.properties"/>
		<echo message="app_patch - ${app_patch}"/>
		<echo message="jar_patch - ${jar_patch}"/>		
		<echo message="db_patch - ${db_patch}"/>

		<antcall target="set_variables"/>		

		<if>
			<and>
				<equals arg1="${jar_patch}" arg2="1"/>
				<equals arg1="${app_patch}" arg2="1"/>
				<equals arg1="${db_patch}" arg2="1"/>
			</and>
				<then>

					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="==================== Deploy+ will be done for DB, JAR and APP patch ======================"/>
					<echo message="===================================== ! Info ! ==========================================="/>					
					

					<loadproperties srcFile="${app_build_property_file}"/>	
					<loadproperties srcFile="${db_build_property_file}"/>
					<loadproperties srcFile="${branch_to_build_property_file}"/>
					<loadproperties srcFile="${active_deploy_env_property_file}"/>		
					<propertycopy name="package_name" from="package.name"/>
					<propertycopy name="db_package_name" from="db.package.name"/>
					<propertycopy name="branch_name" from="git_branch"/>
					<propertycopy name="host_name" from="hostname"/>														

					<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp"/>
					<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${db_package_name}/${host_name}/tmp"/>	

					<antcall target="install_db_patch"/>						
					<antcall target="JAR_File_Upload"/>					
					<antcall target="scp_app_binary"/>					
					<antcall target="app_binary_unpack"/>
					<antcall target="app_binary_install"/>

					<if>
						<equals arg1="${MD120_JSP_Installation_xxfi_i673}" arg2="1"/>
							<then>
								<antcall target="MD120_i673_install"/>
							</then>
						<else>
							<echo message="===================================== ! Info ! ==========================================="/>
							<echo message="=================== Not Applicable - MD120_i673_install - Not Applicable ================="/>
							<echo message="===================================== ! Info ! ==========================================="/>	
						</else>				
					</if>

					<if>
						<equals arg1="${MD120_JSP_Installation_xxfe_f694_f695}" arg2="1"/>
							<then>
								<antcall target="MD120_f694_f695_install"/>
							</then>
						<else>
							<echo message="===================================== ! Info ! ==========================================="/>
							<echo message="================ Not Applicable - MD120_f694_f695_install - Not Applicable ==============="/>
							<echo message="===================================== ! Info ! ==========================================="/>	
						</else>				
					</if>																		
				</then>
		<elseif>
			<and>
				<equals arg1="${jar_patch}" arg2="1"/>
				<equals arg1="${app_patch}" arg2="1"/>
			</and>
				<then>

					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="======================= Deploy+ will be done for JAR and APP patch ======================="/>
					<echo message="===================================== ! Info ! ==========================================="/>

					<loadproperties srcFile="${app_build_property_file}"/>	
					<loadproperties srcFile="${branch_to_build_property_file}"/>
					<loadproperties srcFile="${active_deploy_env_property_file}"/>		
					<propertycopy name="package_name" from="package.name"/>
					<propertycopy name="branch_name" from="git_branch"/>
					<propertycopy name="host_name" from="hostname"/>														

					<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp"/>
					
					<antcall target="JAR_File_Upload"/>
					<antcall target="scp_app_binary"/>
					<antcall target="app_binary_unpack"/>
					<antcall target="app_binary_install"/>

					<if>
						<equals arg1="${MD120_JSP_Installation_xxfi_i673}" arg2="1"/>
							<then>
								<antcall target="MD120_i673_install"/>
							</then>
						<else>
							<echo message="===================================== ! Info ! ==========================================="/>
							<echo message="=================== Not Applicable - MD120_i673_install - Not Applicable ================="/>
							<echo message="===================================== ! Info ! ==========================================="/>	
						</else>				
					</if>

					<if>
						<equals arg1="${MD120_JSP_Installation_xxfe_f694_f695}" arg2="1"/>
							<then>
								<antcall target="MD120_f694_f695_install"/>
							</then>
						<else>
							<echo message="===================================== ! Info ! ==========================================="/>
							<echo message="================ Not Applicable - MD120_f694_f695_install - Not Applicable ==============="/>
							<echo message="===================================== ! Info ! ==========================================="/>	
						</else>				
					</if>							

				</then>
		</elseif>
		<elseif>
				<and>
					<equals arg1="${jar_patch}" arg2="1"/>
					<equals arg1="${db_patch}" arg2="1"/>
				</and>
					<then>

						<echo message="===================================== ! Info ! ==========================================="/>
						<echo message="======================= Deploy+ will be done for JAR and DB patch ========================"/>
						<echo message="===================================== ! Info ! ==========================================="/>

						<loadproperties srcFile="${app_build_property_file}"/>	
						<loadproperties srcFile="${db_build_property_file}"/>
						<loadproperties srcFile="${branch_to_build_property_file}"/>
						<loadproperties srcFile="${active_deploy_env_property_file}"/>		
						<propertycopy name="package_name" from="package.name"/>
						<propertycopy name="db_package_name" from="db.package.name"/>
						<propertycopy name="branch_name" from="git_branch"/>
						<propertycopy name="host_name" from="hostname"/>														

						<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp"/>
						<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${db_package_name}/${host_name}/tmp"/>							

						<antcall target="install_db_patch"/>						
						<antcall target="JAR_File_Upload"/>						

					</then>
		</elseif>
		<elseif>
				<and>
					<equals arg1="${app_patch}" arg2="1"/>
					<equals arg1="${db_patch}" arg2="1"/>
				</and>
					<then>

						<echo message="===================================== ! Info ! ==========================================="/>
						<echo message="======================= Deploy+ will be done for DB and APP patch ========================"/>
						<echo message="===================================== ! Info ! ==========================================="/>

						<loadproperties srcFile="${app_build_property_file}"/>	
						<loadproperties srcFile="${db_build_property_file}"/>
						<loadproperties srcFile="${branch_to_build_property_file}"/>
						<loadproperties srcFile="${active_deploy_env_property_file}"/>		
						<propertycopy name="package_name" from="package.name"/>
						<propertycopy name="db_package_name" from="db.package.name"/>
						<propertycopy name="branch_name" from="git_branch"/>
						<propertycopy name="host_name" from="hostname"/>														

						<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp"/>
						<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${db_package_name}/${host_name}/tmp"/>												
					
						<antcall target="install_db_patch"/>											
						<antcall target="scp_app_binary"/>						
						<antcall target="app_binary_unpack"/>
						<antcall target="app_binary_install"/>

						<if>
							<equals arg1="${MD120_JSP_Installation_xxfi_i673}" arg2="1"/>
								<then>
									<antcall target="MD120_i673_install"/>
								</then>
							<else>
								<echo message="===================================== ! Info ! ==========================================="/>
								<echo message="=================== Not Applicable - MD120_i673_install - Not Applicable ================="/>
								<echo message="===================================== ! Info ! ==========================================="/>	
							</else>				
						</if>

						<if>
							<equals arg1="${MD120_JSP_Installation_xxfe_f694_f695}" arg2="1"/>
								<then>
									<antcall target="MD120_f694_f695_install"/>
								</then>
							<else>
								<echo message="===================================== ! Info ! ==========================================="/>
								<echo message="================ Not Applicable - MD120_f694_f695_install - Not Applicable ==============="/>
								<echo message="===================================== ! Info ! ==========================================="/>	
							</else>				
						</if>	

					</then>
		</elseif>		
		<elseif>
				<equals arg1="${app_patch}" arg2="1"/>
					<then>

						<echo message="===================================== ! Info ! ==========================================="/>
						<echo message="=========================== Deploy+ will be done for APP patch ==========================="/>
						<echo message="===================================== ! Info ! ==========================================="/>

						<loadproperties srcFile="${app_build_property_file}"/>	
						<loadproperties srcFile="${branch_to_build_property_file}"/>
						<loadproperties srcFile="${active_deploy_env_property_file}"/>		
						<propertycopy name="package_name" from="package.name"/>
						<propertycopy name="branch_name" from="git_branch"/>
						<propertycopy name="host_name" from="hostname"/>														

						<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp"/>								

						<antcall target="scp_app_binary"/>
						<antcall target="app_binary_unpack"/>
						<antcall target="app_binary_install"/>

						<if>
							<equals arg1="${MD120_JSP_Installation_xxfi_i673}" arg2="1"/>
								<then>
									<antcall target="MD120_i673_install"/>
								</then>
							<else>
								<echo message="===================================== ! Info ! ==========================================="/>
								<echo message="=================== Not Applicable - MD120_i673_install - Not Applicable ================="/>
								<echo message="===================================== ! Info ! ==========================================="/>	
							</else>				
						</if>

						<if>
							<equals arg1="${MD120_JSP_Installation_xxfe_f694_f695}" arg2="1"/>
								<then>
									<antcall target="MD120_f694_f695_install"/>
								</then>
							<else>
								<echo message="===================================== ! Info ! ==========================================="/>
								<echo message="================ Not Applicable - MD120_f694_f695_install - Not Applicable ==============="/>
								<echo message="===================================== ! Info ! ==========================================="/>	
							</else>				
						</if>	

				</then>
		</elseif>
		<elseif>
				<equals arg1="${db_patch}" arg2="1"/>
					<then>

						<echo message="===================================== ! Info ! ==========================================="/>
						<echo message="=========================== Deploy+ will be done for DB patch ============================"/>
						<echo message="===================================== ! Info ! ==========================================="/>

						<loadproperties srcFile="${app_build_property_file}"/>	
						<loadproperties srcFile="${db_build_property_file}"/>
						<loadproperties srcFile="${branch_to_build_property_file}"/>
						<loadproperties srcFile="${active_deploy_env_property_file}"/>		
						<propertycopy name="package_name" from="package.name"/>
						<propertycopy name="db_package_name" from="db.package.name"/>
						<propertycopy name="branch_name" from="git_branch"/>
						<propertycopy name="host_name" from="hostname"/>														

						<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp"/>
						<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${db_package_name}/${host_name}/tmp"/>												
					
						<antcall target="install_db_patch"/>					

					</then>
		</elseif>
		<elseif>
				<equals arg1="${jar_patch}" arg2="1"/>
					<then>

						<echo message="===================================== ! Info ! ==========================================="/>
						<echo message="========================== Deploy+ will be done for JAR patch ============================"/>
						<echo message="===================================== ! Info ! ==========================================="/>					
					

						<loadproperties srcFile="${app_build_property_file}"/>	
						<loadproperties srcFile="${branch_to_build_property_file}"/>
						<loadproperties srcFile="${active_deploy_env_property_file}"/>		
						<propertycopy name="package_name" from="package.name"/>
						<propertycopy name="branch_name" from="git_branch"/>
						<propertycopy name="host_name" from="hostname"/>														

						<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp"/>

						<antcall target="JAR_File_Upload"/>					

					</then>
		</elseif>								
		</if>
      
		<!-- start of compress binaries to be uploaded artifactory -->
		<!--loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/-->			

		<antcall target="tar">			
			<param name="dir_to_compress" value="${installation_logs}/${branch_name}/${package_name}"/>			
			<param name="destination" value="${installation_logs}/${branch_name}"/>
		</antcall>
		<!-- end of compress binaries to be uploaded artifactory -->

		<!-- start of upload binaries to artifactory -->
		<loadproperties srcFile="${build_property_file}"/>
		<propertycopy name="build_target" from="build_target"/>

		<if>
			<equals arg1="${build_target_temp_var}" arg2="p" casesensitive="No"/>
				<then>
					<propertycopy name="artifactory_host" from="artifactory.prod.host"/>
					<propertycopy name="artifactory_uid" from="artifactory.username"/>
					<propertycopy name="artifactory_pwd" from="artifactory.password"/>
				</then>
			<else>
				<propertycopy name="artifactory_host" from="artifactory.test.host"/>
				<propertycopy name="artifactory_uid" from="artifactory.username"/>
				<propertycopy name="artifactory_pwd" from="artifactory.password"/>				
			</else>
		</if>

		<antcall target="upload_to_artifactory">			
			<param name="binary_file" value="${installation_logs}/${branch_name}/${package_name}.tar.gz"/>
			<param name="destination" value="${artifactory_host}"/>
			<param name="uid" value="${artifactory_uid}"/>
			<param name="pwd" value="${artifactory_pwd}"/>						
		</antcall>

		<antcall target="checkfile_at_artificatory">			
			<param name="binary_file" value="${package_name}.tar.gz"/>
			<param name="destination" value="${artifactory_host}"/>
			<param name="uid" value="${artifactory_uid}"/>
			<param name="pwd" value="${artifactory_pwd}"/>						
		</antcall>

		<loadfile property="file" srcfile="${transit_point}/checkfile_at_artifactory_output.log"/>
		<propertyregex property="status_code" input="${file}" regexp="(HTTP\/1.1.)(\d+)" select="\2" override="true"/>
		<echo message="status_code: ${status_code}"/>

		<if>
			<equals arg1="${status_code}" arg2="200"/>
				<then>
					<echo message=""/>
					<shellscript shell="bash">
						touch "${artifactory_file_status_property_file}"
						./utility/setprop.sh "file_available_at_artifactory" "0" "${artifactory_file_status_property_file}"
					</shellscript>						
				</then>
			<else>
				<echo message="===================================== ! Alert ! =========================================="/>			
				<echo message="============ Error reading file availability. Require manual intervention ================"/>
				<echo message="===================================== ! Alert ! =========================================="/>	
				<shellscript shell="bash">
					touch "${artifactory_file_status_property_file}"
					./utility/setprop.sh "file_available_at_artifactory" "E" "${artifactory_file_status_property_file}"
				</shellscript>										
			</else>
		</if>
		<!-- end of upload binaries to artifactory -->											

		<!-- start of presentation of intallation summary -->
		<antcall target="installation_summary"/>
		<!-- end of presentation of intallation summary -->		

		<echo message=""/>				
		<echo message="B_End: ${clock}"/>

		<echo message=""/>				
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="============================================================ deploy_to_env ========================================================"/>
		<echo message="==================================================================================================================================="/>		
	</target>	

	<target name="deploy_and_park" depends="clock">

		<echo message="==================================================================================================================================="/>
		<echo message="=========================================================== deploy_and_park ======================================================="/>					
		<echo message=""/>
		<echo message="B_Start: ${clock}"/>		
		<echo message=""/>

		<antcall target="installation_summary"/>

		<shellscript shell="bash">
			cp tmp/installation_status.properties tmp/installation_status_bkup.properties
        </shellscript>				

		<property file="${tmp}/installation_to_include.properties"/>
		<echo message="app_patch - ${app_patch}"/>
		<echo message="jar_patch - ${jar_patch}"/>		
		<echo message="db_patch - ${db_patch}"/>

		<if>
			<and>
				<equals arg1="${jar_patch}" arg2="1"/>
				<equals arg1="${app_patch}" arg2="1"/>
				<equals arg1="${db_patch}" arg2="1"/>
			</and>
				<then>

					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="==================== Deploy_and_Park will be done for DB, JAR and APP patch =============="/>
					<echo message="===================================== ! Info ! ==========================================="/>

					<loadproperties srcFile="${app_build_property_file}"/>	
					<loadproperties srcFile="${db_build_property_file}"/>
					<loadproperties srcFile="${branch_to_build_property_file}"/>
					<loadproperties srcFile="${active_deploy_env_property_file}"/>		
					<propertycopy name="package_name" from="package.name"/>
					<propertycopy name="db_package_name" from="db.package.name"/>
					<propertycopy name="branch_name" from="git_branch"/>
					<propertycopy name="host_name" from="hostname"/>														

					<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp"/>
					<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${db_package_name}/${host_name}/tmp"/>											
					
					<antcall target="install_db_patch"/>						
					<antcall target="JAR_File_Upload"/>					
					<antcall target="scp_app_binary"/>					
					<antcall target="app_binary_unpack"/>
					<antcall target="app_binary_install"/>

					<if>
						<equals arg1="${MD120_JSP_Installation_xxfi_i673}" arg2="1"/>
							<then>
								<antcall target="MD120_i673_install"/>
							</then>
						<else>
							<echo message="===================================== ! Info ! ==========================================="/>
							<echo message="=================== Not Applicable - MD120_i673_install - Not Applicable ================="/>
							<echo message="===================================== ! Info ! ==========================================="/>	
						</else>				
					</if>

					<if>
						<equals arg1="${MD120_JSP_Installation_xxfe_f694_f695}" arg2="1"/>
							<then>
								<antcall target="MD120_f694_f695_install"/>
							</then>
						<else>
							<echo message="===================================== ! Info ! ==========================================="/>
							<echo message="================ Not Applicable - MD120_f694_f695_install - Not Applicable ==============="/>
							<echo message="===================================== ! Info ! ==========================================="/>	
						</else>				
					</if>	
				
				</then>
		<elseif>
			<and>
				<equals arg1="${jar_patch}" arg2="1"/>
				<equals arg1="${app_patch}" arg2="1"/>
			</and>
				<then>
					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="================== Deploy_and_Park will be done for JAR and APP patch ===================="/>
					<echo message="===================================== ! Info ! ==========================================="/>

					<loadproperties srcFile="${app_build_property_file}"/>	
					<loadproperties srcFile="${branch_to_build_property_file}"/>
					<loadproperties srcFile="${active_deploy_env_property_file}"/>		
					<propertycopy name="package_name" from="package.name"/>
					<propertycopy name="branch_name" from="git_branch"/>
					<propertycopy name="host_name" from="hostname"/>														

					<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp"/>						
					
					<antcall target="JAR_File_Upload"/>
					<antcall target="scp_app_binary"/>
					<antcall target="app_binary_unpack"/>
					<antcall target="app_binary_install"/>

					<if>
						<equals arg1="${MD120_JSP_Installation_xxfi_i673}" arg2="1"/>
							<then>
								<antcall target="MD120_i673_install"/>
							</then>
						<else>
							<echo message="===================================== ! Info ! ==========================================="/>
							<echo message="=================== Not Applicable - MD120_i673_install - Not Applicable ================="/>
							<echo message="===================================== ! Info ! ==========================================="/>	
						</else>				
					</if>

					<if>
						<equals arg1="${MD120_JSP_Installation_xxfe_f694_f695}" arg2="1"/>
							<then>
								<antcall target="MD120_f694_f695_install"/>
							</then>
						<else>
							<echo message="===================================== ! Info ! ==========================================="/>
							<echo message="================ Not Applicable - MD120_f694_f695_install - Not Applicable ==============="/>
							<echo message="===================================== ! Info ! ==========================================="/>	
						</else>				
					</if>

				</then>
		</elseif>
		<elseif>
				<and>
					<equals arg1="${jar_patch}" arg2="1"/>
					<equals arg1="${db_patch}" arg2="1"/>
				</and>
					<then>

						<echo message="===================================== ! Info ! ==========================================="/>
						<echo message="==================== Deploy_and_Park will be done for JAR and DB patch ==================="/>
						<echo message="===================================== ! Info ! ==========================================="/>

						<loadproperties srcFile="${app_build_property_file}"/>	
						<loadproperties srcFile="${db_build_property_file}"/>
						<loadproperties srcFile="${branch_to_build_property_file}"/>
						<loadproperties srcFile="${active_deploy_env_property_file}"/>		
						<propertycopy name="package_name" from="package.name"/>
						<propertycopy name="db_package_name" from="db.package.name"/>
						<propertycopy name="branch_name" from="git_branch"/>
						<propertycopy name="host_name" from="hostname"/>														

						<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp"/>
						<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${db_package_name}/${host_name}/tmp"/>							

						<antcall target="install_db_patch"/>						
						<antcall target="JAR_File_Upload"/>					

					</then>
		</elseif>
		<elseif>
				<and>
					<equals arg1="${app_patch}" arg2="1"/>
					<equals arg1="${db_patch}" arg2="1"/>
				</and>
					<then>

						<echo message="===================================== ! Info ! ==========================================="/>
						<echo message="==================== Deploy_and_Park will be done for DB and APP patch ==================="/>
						<echo message="===================================== ! Info ! ==========================================="/>

						<loadproperties srcFile="${app_build_property_file}"/>	
						<loadproperties srcFile="${db_build_property_file}"/>
						<loadproperties srcFile="${branch_to_build_property_file}"/>
						<loadproperties srcFile="${active_deploy_env_property_file}"/>		
						<propertycopy name="package_name" from="package.name"/>
						<propertycopy name="db_package_name" from="db.package.name"/>
						<propertycopy name="branch_name" from="git_branch"/>
						<propertycopy name="host_name" from="hostname"/>														

						<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp"/>
						<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${db_package_name}/${host_name}/tmp"/>												
					
						<antcall target="install_db_patch"/>										
						<antcall target="scp_app_binary"/>						
						<antcall target="app_binary_unpack"/>
						<antcall target="app_binary_install"/>

						<if>
							<equals arg1="${MD120_JSP_Installation_xxfi_i673}" arg2="1"/>
								<then>
									<antcall target="MD120_i673_install"/>
								</then>
							<else>
								<echo message="===================================== ! Info ! ==========================================="/>
								<echo message="=================== Not Applicable - MD120_i673_install - Not Applicable ================="/>
								<echo message="===================================== ! Info ! ==========================================="/>	
							</else>				
						</if>

						<if>
							<equals arg1="${MD120_JSP_Installation_xxfe_f694_f695}" arg2="1"/>
								<then>
									<antcall target="MD120_f694_f695_install"/>
								</then>
							<else>
								<echo message="===================================== ! Info ! ==========================================="/>
								<echo message="================ Not Applicable - MD120_f694_f695_install - Not Applicable ==============="/>
								<echo message="===================================== ! Info ! ==========================================="/>	
							</else>				
						</if>

					</then>
		</elseif>		
		<elseif>
				<equals arg1="${app_patch}" arg2="1"/>
					<then>

						<echo message="===================================== ! Info ! ==========================================="/>
						<echo message="======================== Deploy_and_Park will be done for APP patch ======================"/>
						<echo message="===================================== ! Info ! ==========================================="/>

						<loadproperties srcFile="${app_build_property_file}"/>	
						<loadproperties srcFile="${branch_to_build_property_file}"/>
						<loadproperties srcFile="${active_deploy_env_property_file}"/>		
						<propertycopy name="package_name" from="package.name"/>
						<propertycopy name="branch_name" from="git_branch"/>
						<propertycopy name="host_name" from="hostname"/>														

						<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp"/>

						<antcall target="scp_app_binary"/>
						<antcall target="app_binary_unpack"/>
						<antcall target="app_binary_install"/>

						<if>
							<equals arg1="${MD120_JSP_Installation_xxfi_i673}" arg2="1"/>
								<then>
									<antcall target="MD120_i673_install"/>
								</then>
							<else>
								<echo message="===================================== ! Info ! ==========================================="/>
								<echo message="=================== Not Applicable - MD120_i673_install - Not Applicable ================="/>
								<echo message="===================================== ! Info ! ==========================================="/>	
							</else>				
						</if>

						<if>
							<equals arg1="${MD120_JSP_Installation_xxfe_f694_f695}" arg2="1"/>
								<then>
									<antcall target="MD120_f694_f695_install"/>
								</then>
							<else>
								<echo message="===================================== ! Info ! ==========================================="/>
								<echo message="================ Not Applicable - MD120_f694_f695_install - Not Applicable ==============="/>
								<echo message="===================================== ! Info ! ==========================================="/>	
							</else>				
						</if>	

				</then>
		</elseif>
		<elseif>
				<equals arg1="${db_patch}" arg2="1"/>
					<then>

						<echo message="===================================== ! Info ! ==========================================="/>
						<echo message="======================== Deploy_and_Park will be done for DB patch ======================="/>
						<echo message="===================================== ! Info ! ==========================================="/>

						<loadproperties srcFile="${app_build_property_file}"/>	
						<loadproperties srcFile="${db_build_property_file}"/>
						<loadproperties srcFile="${branch_to_build_property_file}"/>
						<loadproperties srcFile="${active_deploy_env_property_file}"/>		
						<propertycopy name="package_name" from="package.name"/>
						<propertycopy name="db_package_name" from="db.package.name"/>
						<propertycopy name="branch_name" from="git_branch"/>
						<propertycopy name="host_name" from="hostname"/>														

						<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp"/>
						<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${db_package_name}/${host_name}/tmp"/>												
					
						<antcall target="install_db_patch"/>	

					</then>
		</elseif>
		<elseif>
				<equals arg1="${jar_patch}" arg2="1"/>
					<then>

						<echo message="===================================== ! Info ! ==========================================="/>
						<echo message="======================= Deploy_and_Park will be done for JAR patch ======================="/>
						<echo message="===================================== ! Info ! ==========================================="/>

						<loadproperties srcFile="${app_build_property_file}"/>	
						<loadproperties srcFile="${branch_to_build_property_file}"/>
						<loadproperties srcFile="${active_deploy_env_property_file}"/>		
						<propertycopy name="package_name" from="package.name"/>
						<propertycopy name="branch_name" from="git_branch"/>
						<propertycopy name="host_name" from="hostname"/>														

						<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp"/>
					
						<antcall target="JAR_File_Upload"/>					

					</then>
		</elseif>								
		</if>
      
		<!-- start of compress binaries to be uploaded artifactory -->
		<!--loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/-->			

		<antcall target="tar">			
			<param name="dir_to_compress" value="${installation_logs}/${branch_name}/${package_name}"/>			
			<param name="destination" value="${installation_logs}/${branch_name}"/>
		</antcall>
		<!-- end of compress binaries to be uploaded artifactory -->

		<!-- start of upload binaries to artifactory -->
		<loadproperties srcFile="${build_property_file}"/>
		<propertycopy name="build_target" from="build_target"/>

		<if>
			<equals arg1="${build_target_temp_var}" arg2="p" casesensitive="No"/>
				<then>
					<propertycopy name="artifactory_host" from="artifactory.prod.host"/>
					<propertycopy name="artifactory_uid" from="artifactory.username"/>
					<propertycopy name="artifactory_pwd" from="artifactory.password"/>
				</then>
			<else>
				<propertycopy name="artifactory_host" from="artifactory.test.host"/>
				<propertycopy name="artifactory_uid" from="artifactory.username"/>
				<propertycopy name="artifactory_pwd" from="artifactory.password"/>				
			</else>
		</if>

		<antcall target="upload_to_artifactory">			
			<param name="binary_file" value="${installation_logs}/${branch_name}/${package_name}.tar.gz"/>
			<param name="destination" value="${artifactory_host}"/>
			<param name="uid" value="${artifactory_uid}"/>
			<param name="pwd" value="${artifactory_pwd}"/>						
		</antcall>

		<antcall target="checkfile_at_artificatory">			
			<param name="binary_file" value="${package_name}.tar.gz"/>
			<param name="destination" value="${artifactory_host}"/>
			<param name="uid" value="${artifactory_uid}"/>
			<param name="pwd" value="${artifactory_pwd}"/>						
		</antcall>

		<loadfile property="file" srcfile="${transit_point}/checkfile_at_artifactory_output.log"/>
		<propertyregex property="status_code" input="${file}" regexp="(HTTP\/1.1.)(\d+)" select="\2" override="true"/>
		<echo message="status_code: ${status_code}"/>

		<if>
			<equals arg1="${status_code}" arg2="200"/>
				<then>
					<echo message=""/>
					<shellscript shell="bash">
						touch "${artifactory_file_status_property_file}"
						./utility/setprop.sh "file_available_at_artifactory" "0" "${artifactory_file_status_property_file}"
					</shellscript>						
				</then>
			<else>
				<echo message="===================================== ! Alert ! =========================================="/>			
				<echo message="============ Error reading file availability. Require manual intervention ================"/>
				<echo message="===================================== ! Alert ! =========================================="/>	
				<shellscript shell="bash">
					touch "${artifactory_file_status_property_file}"
					./utility/setprop.sh "file_available_at_artifactory" "E" "${artifactory_file_status_property_file}"
				</shellscript>										
			</else>
		</if>
		<!-- end of upload binaries to artifactory -->											

		<!-- start of presentation of intallation summary -->
		<antcall target="installation_summary"/>
		<!-- end of presentation of intallation summary -->		

		<echo message=""/>				
		<echo message="B_End: ${clock}"/>


		<echo message=""/>				
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="=========================================================== deploy_and_park ======================================================="/>
		<echo message="==================================================================================================================================="/>		
	</target>

	<target name="deploy_continue" depends="clock">

		<echo message="==================================================================================================================================="/>
		<echo message="=========================================================== deploy_contiue ========================================================"/>					
		<echo message=""/>
		<echo message="B_Start: ${clock}"/>		
		<echo message=""/>

		<antcall target="tomcat_restart"/>	
		<antcall target="middle_tier_restart"/>					
		<antcall target="cm_restart"/>	

		<echo message=""/>				
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="=========================================================== deploy_contiue ========================================================"/>		
		<echo message="==================================================================================================================================="/>		
	</target>


	<target name="build_checkout" depends="clock">

		<echo message="==================================================================================================================================="/>
		<echo message="============================================================ build_checkout ======================================================="/>					
		<echo message=""/>
		<echo message="B_Start: ${clock}"/>		
		<echo message=""/>

		<antcall target="branch_checkout_master"/>
		<antcall target="branch_checkout"/>

		<property file="${tmp}/installation_to_include.properties"/>
		<echo message="app_patch - ${app_patch}"/>
		<echo message="jar_patch - ${jar_patch}"/>		
		<echo message="db_patch - ${db_patch}"/>

		<if>
			<and>
				<equals arg1="${jar_patch}" arg2="1"/>
				<equals arg1="${app_patch}" arg2="1"/>
				<equals arg1="${db_patch}" arg2="1"/>
			</and>
				<then>

					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="============================ DB, JAR and APP build checkout =============================="/>
					<echo message="===================================== ! Info ! ==========================================="/>		

					<antcall target="jar_build_checkout"/>

					<antcall target="app_build_checkout"/>					
					<antcall target="setup_package"/>

					<antcall target="db_build_checkout"/>					
					<antcall target="setup_db_package"/>					

				</then>
		<elseif>
			<and>
				<equals arg1="${jar_patch}" arg2="1"/>
				<equals arg1="${app_patch}" arg2="1"/>
			</and>
				<then>

					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="============================== JAR and APP build checkout ================================"/>
					<echo message="===================================== ! Info ! ==========================================="/>					

					<antcall target="jar_build_checkout"/>

					<antcall target="app_build_checkout"/>					
					<antcall target="setup_package"/>

				</then>
		</elseif>
		<elseif>
				<and>
					<equals arg1="${jar_patch}" arg2="1"/>
					<equals arg1="${db_patch}" arg2="1"/>
				</and>
					<then>

					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="============================== DB and JAR build checkout ================================="/>
					<echo message="===================================== ! Info ! ==========================================="/>						

					<antcall target="jar_build_checkout"/>

					<antcall target="db_build_checkout"/>					
					<antcall target="setup_db_package"/>					

					</then>
		</elseif>
		<elseif>
				<and>
					<equals arg1="${app_patch}" arg2="1"/>
					<equals arg1="${db_patch}" arg2="1"/>
				</and>
					<then>

					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="============================== DB and APP build checkout ================================="/>
					<echo message="===================================== ! Info ! ==========================================="/>						

					<antcall target="app_build_checkout"/>					
					<antcall target="setup_package"/>

					<antcall target="db_build_checkout"/>					
					<antcall target="setup_db_package"/>	

					</then>
		</elseif>		
		<elseif>
				<equals arg1="${app_patch}" arg2="1"/>
					<then>

					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="============================== APP build checkout ========================================"/>
					<echo message="===================================== ! Info ! ==========================================="/>						

					<antcall target="app_build_checkout"/>						
					<antcall target="setup_package"/>						

				</then>
		</elseif>
		<elseif>
				<equals arg1="${db_patch}" arg2="1"/>
					<then>

					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="================================= DB build checkout ======================================"/>
					<echo message="===================================== ! Info ! ==========================================="/>						

					<antcall target="db_build_checkout"/>					
					<antcall target="setup_db_package"/>	

					</then>
		</elseif>
		<elseif>
				<equals arg1="${jar_patch}" arg2="1"/>
					<then>

					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="================================= JAR build checkout ====================================="/>
					<echo message="===================================== ! Info ! ==========================================="/>						

					<antcall target="jar_build_checkout"/>

				</then>
		</elseif>								
		</if>

		<echo message=""/>				
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="============================================================ build_checkout ======================================================="/>
		<echo message="==================================================================================================================================="/>		
	</target>


	<target name="rollback_build_checkout" depends="clock">

		<echo message="==================================================================================================================================="/>
		<echo message="======================================================= rollback_build_checkout ==================================================="/>					
		<echo message=""/>
		<echo message="B_Start: ${clock}"/>		
		<echo message=""/>

		<antcall target="branch_checkout_master"/>

		<property file="${tmp}/installation_to_include.properties"/>
		<echo message="app_patch - ${app_patch}"/>
		<echo message="jar_patch - ${jar_patch}"/>		
		<echo message="db_patch - ${db_patch}"/>

		<if>
			<and>
				<equals arg1="${jar_patch}" arg2="1"/>
				<equals arg1="${app_patch}" arg2="1"/>
				<equals arg1="${db_patch}" arg2="1"/>
			</and>
				<then>
					
					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="============================ DB, JAR and APP build checkout =============================="/>
					<echo message="===================================== ! Info ! ==========================================="/>	

					<antcall target="jar_build_checkout"/>

					<antcall target="app_build_checkout"/>					

					<antcall target="db_build_checkout"/>					

				</then>
		<elseif>
			<and>
				<equals arg1="${jar_patch}" arg2="1"/>
				<equals arg1="${app_patch}" arg2="1"/>
			</and>
				<then>

					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="============================== JAR and APP build checkout ================================"/>
					<echo message="===================================== ! Info ! ==========================================="/>	

					<antcall target="jar_build_checkout"/>

					<antcall target="app_build_checkout"/>					

				</then>
		</elseif>
		<elseif>
				<and>
					<equals arg1="${jar_patch}" arg2="1"/>
					<equals arg1="${db_patch}" arg2="1"/>
				</and>
					<then>

						<echo message="===================================== ! Info ! ==========================================="/>
						<echo message="============================== DB and JAR build checkout ================================="/>
						<echo message="===================================== ! Info ! ==========================================="/>						
						<antcall target="jar_build_checkout"/>

						<antcall target="db_build_checkout"/>					

					</then>
		</elseif>
		<elseif>
				<and>
					<equals arg1="${app_patch}" arg2="1"/>
					<equals arg1="${db_patch}" arg2="1"/>
				</and>
					<then>

						<echo message="===================================== ! Info ! ==========================================="/>
						<echo message="============================== DB and APP build checkout ================================="/>
						<echo message="===================================== ! Info ! ==========================================="/>						

						<antcall target="app_build_checkout"/>					

						<antcall target="db_build_checkout"/>					

					</then>
		</elseif>		
		<elseif>
				<equals arg1="${app_patch}" arg2="1"/>
					<then>

						<echo message="===================================== ! Info ! ==========================================="/>
						<echo message="============================== APP build checkout ========================================"/>
						<echo message="===================================== ! Info ! ==========================================="/>						

						<antcall target="app_build_checkout"/>						

				</then>
		</elseif>
		<elseif>
				<equals arg1="${db_patch}" arg2="1"/>
					<then>

						<echo message="===================================== ! Info ! ==========================================="/>
						<echo message="================================= DB build checkout ======================================"/>
						<echo message="===================================== ! Info ! ==========================================="/>						

						<antcall target="db_build_checkout"/>					

					</then>
		</elseif>
		<elseif>
				<equals arg1="${jar_patch}" arg2="1"/>
					<then>

						<echo message="===================================== ! Info ! ==========================================="/>
						<echo message="================================= JAR build checkout ====================================="/>
						<echo message="===================================== ! Info ! ==========================================="/>						

						<antcall target="jar_build_checkout"/>

					</then>
		</elseif>								
		</if>

		<echo message=""/>				
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="======================================================= rollback_build_checkout ==================================================="/>
		<echo message="==================================================================================================================================="/>		
	</target>		


<!-- End of targets to auto-deploy -->

<!-- Start of targets for app build -->
<target name="setup_package">
<echo message=""/>
<echo message="==================================================================================================================================="/>
<echo message="============================================================ setup_package ========================================================"/>
<!--input message="Please enter the package name?" addProperty="package.name"/>
<shellscript shell="bash">
echo package.name=${package.name} > ./tmp/temp.properties
</shellscript-->
<shellscript shell="bash">
git_branch=`grep 'git_branch' "${branch_to_build_property_file}" | cut -d '=' -f 2`;
echo "${git_branch}";
build_target=`grep "build_target" "${branch_to_build_property_file}" | cut -d '=' -f 2`;
echo "${build_target}";
touch "${app_build_property_file}"
if [[ "${build_target}" == "t" ]]; then
build_number=`grep ""${git_branch}"-TestBuildNumber" "${branch_version_property_file}" | cut -d '=' -f 2`;
./utility/setprop.sh "package.name" "${git_branch}-BLD${build_number}" "${app_build_property_file}";
elif [[ "${build_target}" == "p" ]]; then
build_number=`grep ""${git_branch}"-ProdBuildNumber" "${branch_version_property_file}" | cut -d '=' -f 2`;
./utility/setprop.sh "package.name" "${git_branch}-RC${build_number}" "${app_build_property_file}";
fi
</shellscript>
  
  		<java classname="Build" classpath="${classes}">
			<arg value="do-not-update"/>
		</java>	

		<antcall target="setup-partial-install"/>

		<echo message="============================================================ setup_package ========================================================"/>
		<echo message="==================================================================================================================================="/>		
	</target>

	<target name="setup-partial-install" description="Setup partial installation for delivery codes defined in partials.lst - file">
		<echo message="==================================================================================================================================="/>	
		<echo message="======================================================== setup-partial-install ===================================================="/>
		<fail message="temp.properties not available">
		  <condition>
		   <not>
		     <available file="tmp/temp.properties" property="temp.property.present" />
		   </not>
		  </condition>
		</fail>
		<shellscript shell="bash">
		 #created during setup-test/prod-package
		 
         echo -ne "\n"
         temp_properties=tmp/temp.properties
		 echo "Build Info"
		 echo "${temp_properties[@]}"
         cat "${temp_properties}"          

         echo -ne "\n"
         echo "Extracting package name for further processing"
		 package_name=`grep package.name ${temp_properties} | cut -d '=' -f 2`
		 echo "package_name: ${package_name}"
		 
         echo -ne "\n"
         echo "Extracting delivery codes included for further processing"
         PKGLIST=tmp/ceb_pack_$package_name.lst 
		 echo "Package delivery codes from: ${PKGLIST[@]}"
         cat "${PKGLIST}"
		 
         #this list contains the deliverycodes 
		 #that are to be changed f->p
		 #list in form:
		 #xxfe_f305
		 #xxfi_i etc...
		 
         echo -ne "\n"
         echo "Extracting delivery codes configured for partial compilation" 
		 PARTIALS=properties/partials.lst
		 echo "Partials delivery codes from: ${PARTIALS[@]}"
		 cat "${PARTIALS[@]}"
         
         ${d2u} "${PKGLIST}"
         ${d2u} "${PARTIALS}"
		 
         echo -ne "\n"
         echo "Initiating iteration variable to hold delivery codes for partial compilation" 
         partialvals=`cat ${PARTIALS}`
         echo "${partialvals[@]}"
          
		 for dc in "${partialvals}" ; do
		 
		 dcfound=`cat "${PKGLIST}" | grep $dc`

		 if [ ! "${dcfound}" = ""  ]; then
		   echo "Replacing ${dc}"
		   sed -i.backup -e "s/$dc f/$dc p/" $PKGLIST
		 else 
		   echo "$dc not found from list, skipped"
		 fi
		 done
		</shellscript>
		<echo message="======================================================== setup-partial-install ===================================================="/>
		<echo message="==================================================================================================================================="/>		
	</target>

		<!--shellscript shell="bash">
			package=`cat ./tmp/temp.properties | grep package.name | cut -d '=' -f 2`
			# remove linefeeds from the properties file
			dos2unix build.properties
			  rm build/*.*-->	

	<target name="make_package">
		<echo message="==================================================================================================================================="/>
		<echo message="============================================================ make_package ========================================================="/>		
		<shellscript shell="bash">
			package=`cat ./tmp/temp.properties | grep package.name | cut -d '=' -f 2`
			# remove linefeeds from the properties file

		    rm build/*.*  

		    chmod 777 build

            deliverycodes=`cat tmp/ceb_pack_${package}.lst | cut -d '_' -f 2 | cut -d ' ' -f 1`
              
            cp tmp/ceb_pack_${package}.lst build/ceb_pack_${package}.lst
                          
            chmod -R 777 build

            cd build
              
			echo --------------------------------------------------
			echo ----------------------DOS2UNIX--------------------
			echo --------------------------------------------------
			find . -exec dos2unix --safe {} \;
			echo --------------------------------------------------
			echo -------------------END OF DOS2UNIX----------------
			echo --------------------------------------------------
                  for deliverycode in $deliverycodes ; do
                  	deliverycode_u=`echo $deliverycode | tr [a-z] [A-Z]`
                  	module=`cat ../tmp/temp.properties | grep $deliverycode | cut -d '=' -f 2 | tr [a-z] [A-Z]`
                  	find . -maxdepth 4 -name $deliverycode -exec tar -c -Hustar -v -fMD110_$module_$deliverycode_u.tar {} \;
			### tar cvf MD110_$module$deliverycode_u.tar 
                  	gzip MD110_$module_$deliverycode_u.tar 
                  done
                  
                  tar cvf CEB_PACK_${package}.tar *.*
                  gzip CEB_PACK_${package}.tar
			rm MD110*
        </shellscript>

        <!-- find . -exec ${d2u} dash dash safe {} \; For windows machine, d2u refers to place where docker utility is installed on the machine-->		

		<unset file="${branch_to_build_property_file}"/>
		<unset file="${app_build_property_file}"/>			
		<var name="branch_name" unset="true"/>
		<var name="package_name" unset="true"/>

		<loadproperties srcFile="${active_deploy_env_property_file}"/>
        <loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>         
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="host_name" from="hostname"/>

        <!--copydir src="${build}" dest="${installation_logs}/${branch_name}/${package_name}/binaries/"/ Task deprecated, so replaced with copy task-->

		<mkdir dir="${installation_logs}/${branch_name}/${package_name}/tmp"/>
		<!--mkdir dir="${installation_logs}/${branch_name}/${package_name}-RB/tmp"/-->		
		<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp"/>
		<!--mkdir dir="${installation_logs}/${branch_name}/${package_name}-RB/${host_name}/tmp"/-->		
		<mkdir dir="${installation_logs}/${branch_name}/${package_name}/binaries/build"/>	
		<!--mkdir dir="${installation_logs}/${branch_name}/${package_name}-RB/binaries/build"/-->	        

    	<copy includeemptydirs="false" todir="${installation_logs}/${branch_name}/${package_name}/binaries/build/">
            <fileset dir="${build}">
            	<include name="*.tar.gz"/>
            	<include name="*.lst"/>
            </fileset>            
    	</copy>
    	<copy includeemptydirs="false" todir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp/">
            <fileset dir="${tmp}"/>
    	</copy> 
    	<copy includeemptydirs="false" todir="${installation_logs}/${branch_name}/${package_name}/tmp/">
            <fileset dir="${tmp}"/>
    	</copy>     	 

		<echo message="============================================================ make_package ========================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>

	<target name="setup_db_package"> 
		<echo message="==================================================================================================================================="/>
		<echo message="========================================================== setup_db_package ======================================================="/>		

		<shellscript shell="bash">
				git_branch=`grep 'git_branch' "${branch_to_build_property_file}" | cut -d '=' -f 2`;
				echo "${git_branch}";

				build_target=`grep "build_target" "${branch_to_build_property_file}" | cut -d '=' -f 2`;
				echo "${build_target}";

				touch "${app_build_property_file}"
				touch "${db_build_property_file}"

				if [[ "${build_target}" == "t" ]]; then

					build_number=`grep ""${git_branch}"-TestBuildNumber-DB" "${branch_version_property_file}" | cut -d '=' -f 2`;

					./utility/setprop.sh "package.name" "${git_branch}-BLD${build_number}" "${app_build_property_file}";
					./utility/setprop.sh "db.package.name" "${git_branch}-BLD${build_number}-DB" "${db_build_property_file}";

				elif [[ "${build_target}" == "p" ]]; then

					build_number=`grep ""${git_branch}"-ProdBuildNumber-DB" "${branch_version_property_file}" | cut -d '=' -f 2`;

					./utility/setprop.sh "package.name" "${git_branch}-RC${build_number}" "${app_build_property_file}";
					./utility/setprop.sh "db.package.name" "${git_branch}-RC${build_number}-DB" "${db_build_property_file}";

				fi					
		</shellscript>		

		<java classname="DB_Build" classpath="${classes}">
			<arg value="do-not-update"/>
		</java>

		<mkdir dir="${db_build}/lst"/>	

		<echo message="========================================================== setup_db_package ======================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>	


	<target name="make_db_package">
		<echo message="==================================================================================================================================="/>
		<echo message="=========================================================== make_db_package ======================================================="/>	

		<shellscript shell="bash">
			
			package=`cat ./tmp/temp_db.properties | grep db.package.name | cut -d '=' -f 2`
			# remove linefeeds from the properties file
    	    
    	    rm db_build/*.*      
            
            cp tmp/cebdb_pack_${package}.lst db_build/lst/cebdb_pack_${package}.lst

            chmod -R 777 db_build

		    cd db_build		      

			echo --------------------------------------------------
			echo ----------------------DOS2UNIX--------------------
			echo --------------------------------------------------
			find . -exec dos2unix --safe {} \;
			echo --------------------------------------------------
			echo -------------------END OF DOS2UNIX----------------
			echo --------------------------------------------------

			tar cvzf CEBDB_PACK_${package}.tar.gz *

        </shellscript>

<!-- find . -exec ${d2u} dash dash safe {} \; For windows machine, d2u refers to place where docker utility is installed on the machine-->		

		<unset file="${branch_to_build_property_file}"/>
		<unset file="${app_build_property_file}"/>
		<unset file="${db_build_property_file}"/>			
		<var name="branch_name" unset="true"/>
		<var name="package_name" unset="true"/>
		<var name="db_package_name" unset="true"/>

		<loadproperties srcFile="${app_build_property_file}"/>	
		<loadproperties srcFile="${db_build_property_file}"/>
		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="db_package_name" from="db.package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>

		<mkdir dir="${installation_logs}/${branch_name}/${package_name}/tmp"/>
		<!--mkdir dir="${installation_logs}/${branch_name}/${package_name}-RB/tmp"/-->
		<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${db_package_name}/${host_name}/tmp"/>
		<!--mkdir dir="${installation_logs}/${branch_name}/${package_name}-RB/${db_package_name}-RB/${host_name}/tmp"/-->
		<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${db_package_name}/binaries/db_build/lst"/>	
		<!--mkdir dir="${installation_logs}/${branch_name}/${package_name}-RB/${db_package_name}-RB/binaries/db_build/lst"/-->			

    	<copy includeemptydirs="false" todir="${installation_logs}/${branch_name}/${package_name}/${db_package_name}/binaries/db_build/">
            <fileset dir="${db_build}">
            	<!--include name="*.tar.gz"/-->
  				<patternset>
    				<exclude name="xxfi_db/**"/>
  				</patternset>            	
            </fileset>
    	</copy> 
    	<copy includeemptydirs="false" todir="${installation_logs}/${branch_name}/${package_name}/tmp/">
            <fileset dir="${tmp}"/>
    	</copy> 

    	<copy includeemptydirs="false" todir="${installation_logs}/${branch_name}/${package_name}/${db_package_name}/${host_name}/tmp/">
            <fileset dir="${tmp}"/>
    	</copy>  	


		<echo message="=========================================================== make_db_package ======================================================="/>	
		<echo message="==================================================================================================================================="/>
	</target>

<!-- End of targets for app build -->

<!-- Start of targets for jar build -->
	
	<tstamp>
		<format property="file.date" pattern="yyyy-MM-dd"/>
	</tstamp>
	<tstamp>
		<format property="file.time" pattern="HHmm"/>
	</tstamp>
	<property name="jar.file" value="configurator${file.date}_${file.time}.jar"/>
	<property name="jar.dir" value="./compiled_jar/"/>

    <!--property name="target" value="1.8"/>
    <property name="source" value="1.8"/>
    <property name="compiler" value="javac1.8"/-->    
    <path id="Configurator.classpath">
		<pathelement location="${jar_folder}"/>
		<pathelement location="${jar_folder}/lib/classes12.jar"/>
		<pathelement location="${jar_folder}/lib/collections.jar"/>
		<pathelement location="${jar_folder}/lib/czfnd.jar"/>
		<pathelement location="${jar_folder}/lib/F1J7Swing.jar"/>
		<pathelement location="${jar_folder}/lib/fnd.common.jar"/>
		<pathelement location="${jar_folder}/lib/i483.jar"/>
		<pathelement location="${jar_folder}/lib/javax.sql.jar"/>
		<pathelement location="${jar_folder}/lib/ojdbc14.jar"/>
		<pathelement location="${jar_folder}/lib/oracle.apps.cz.jar"/>
		<pathelement location="${jar_folder}/lib/servlet.jar"/>
		<pathelement location="${jar_folder}/lib/xmlparserv2.jar"/>
    </path>
    <!--path id="lib.path.ref">
  		<fileset dir="C:\Program Files\Java\jre6\lib" includes="*.jar"/>
	</path-->	

	<target name="create_jar">		<!--checkout dc's; version 1.0; 04.08.2008-->
		<echo message="==================================================================================================================================="/>		
		<echo message="============================================================= create_jar =========================================================="/>
		
		<property environment="env"/>
    	<property name="debuglevel" value="source,lines,vars"/>
    	<property name="target" value="1.5"/>
    	<property name="source" value="1.5"/>

	<delete> <!--CLEAN-->
            <fileset dir="./JAR/" includes="**/*.class"/>
			<fileset dir="./JAR/" includes="**/AccessInfoSOAPAdapterDB.java"/>
    </delete>
    <copy includeemptydirs="false" todir="."> <!--INIT-->
            <fileset dir="." excludes="**/*.launch, **/*.java"/>
    </copy>
	<javac includeantruntime="false" debug="true" srcdir="${jar_folder}/" debuglevel="${debuglevel}" destdir="${jarfiles}/" source="${source}" target="${target}" encoding="iso-8859-1">
            <!--src path="."/-->
            <!--compilerarg value="-Xlint:deprecation"/-->
            <classpath refid="Configurator.classpath"/>
            <!--bootclasspath refid="lib.path.ref"/-->
    </javac>
	

	<echo message="Deleting and re-creating directory ${jar.dir}"/>
	<delete dir="${jar.dir}" />
	<!-- <echo message="Creating jar dir into ${jar.dir}"/>-->
	<mkdir dir="${jar.dir}"/>			
		<jar destfile="${jar.dir}${jar.file}" update="false" compress="true">
			<fileset dir="jarfiles" includes="**/*.class"/>
		    <manifest>                        
                <section name="Alpha2 Configurator extensions">
                    <attribute name="Creation-date" value="${file.date}"/>
                    <attribute name="Creation-time" value="${file.time}"/>
                </section>                        
            </manifest>
        </jar>
        <!--  <jar destfile="${jar.file}" includes="**/*.class" update="false" compress="false"> -->
		<antcall target="compile" />

<!--  *** Please ensure to run target 'setup_package' for the below copydir to work *** -->

		<unset file="${branch_to_build_property_file}"/>
		<unset file="${app_build_property_file}"/>			
		<var name="branch_name" unset="true"/>
		<var name="package_name" unset="true"/>

        <loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>         
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="package_name" from="package.name"/>

		<mkdir dir="${installation_logs}/${branch_name}/${package_name}/binaries/jar"/>
		<mkdir dir="${installation_logs}/${branch_name}/${package_name}/tmp"/>
		<!--mkdir dir="${installation_logs}/${branch_name}/${package_name}-RB/tmp"/-->		
		<mkdir dir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp"/>
		<!--mkdir dir="${installation_logs}/${branch_name}/${package_name}-RB/${host_name}/tmp"/-->		
		<mkdir dir="${installation_logs}/${branch_name}/${package_name}/binaries/build"/>	
		<!--mkdir dir="${installation_logs}/${branch_name}/${package_name}-RB/binaries/build"/-->		

    	<copy includeemptydirs="false" todir="${installation_logs}/${branch_name}/${package_name}/binaries/jar/">
            <fileset dir="${jar_compiled_jar}"/>
    	</copy>

    	<copy includeemptydirs="false" todir="${installation_logs}/${branch_name}/${package_name}/${host_name}/tmp/">
            <fileset dir="${tmp}"/>
    	</copy> 
 
    	<copy includeemptydirs="false" todir="${installation_logs}/${branch_name}/${package_name}/tmp/">
            <fileset dir="${tmp}"/>
    	</copy>  

		<echo message="============================================================= create_jar =========================================================="/>	
		<echo message="==================================================================================================================================="/>
	</target>

<!-- End of targets for jar build -->

<!-- Start of targets to install jar patch -->

	<target name="JAR_File_Upload">
		<echo message="==================================================================================================================================="/>
		<echo message="========================================================= JAR_File_Upload ========================================================="/>
		<echo message=""/>
		<echo message="B_Start: ${clock}"/>	
		<echo message=""/>		

		<shellscript shell="bash">

			applicationurl=`grep applicationurl "${active_deploy_env_property_file}" | cut -d '=' -f 2`;
			echo "$applicationurl";
			applicationurl=`echo "$applicationurl" | tr -d '\\'`;
			echo "$applicationurl";

			applicationuser=`grep applicationuser "${active_deploy_env_property_file}" | cut -d '=' -f 2`;
			echo "$applicationuser";

			applicationpasswd=`grep applicationpasswd "${active_deploy_env_property_file}" | cut -d '=' -f 2`;
			echo "$applicationpasswd";

			temp_properties=tmp/temp.properties;
		 	echo "$temp_properties";
		 	#created during setup-test/prod-package;
		 	package_name=`grep package.name $temp_properties | cut -d '=' -f 2`;
		 	echo "$package_name";

			branch_name=`grep 'git_branch' "${branch_to_build_property_file}" | cut -d '=' -f 2`;
			echo "$branch_name";

			echo "==========basename==========";
			cd "${installation_logs}"/"${branch_name}"/"${package_name}"/binaries/jar/;
			basenameforjar=`basename *.jar`;
			jaruplpath1=`echo "${installation_logs}""\\\""${branch_name}""\\\""${package_name}""\\\"binaries"\\\"jar"\\\""${basenameforjar}"`;
			echo "$jaruplpath1";

			echo "==========realname==========";			
			cd "${installation_logs}"/"${branch_name}"/"${package_name}"/binaries/jar/;
			realpath=`realpath *.jar`;

			first=`echo "$realpath" | cut -c2- | cut -c1 | tr [a-z] [A-Z]`;
			second=`echo "$realpath" | cut -c3-`;

			jaruplpath2=`echo $first$second | sed -e "s/\(.\)/\1:/"`;
			echo "$jaruplpath2";

			cd "${robot}";

			robot -T -d reports -v url:"${applicationurl}" -v user:"${applicationuser}" -v password:"${applicationpasswd}" -v uplpath:"${jaruplpath1}" ConfiguratorJARUpload.robot; 			
		
		</shellscript>	

		<!-- 			

			applicationurl=`grep applicationurl "${active_deploy_env_property_file}" | cut -d '=' -f 2`;
			echo "$applicationurl";
			applicationurl=`echo "$applicationurl" | tr -d '\\'`;
			echo "$applicationurl";

			applicationuser=`grep applicationuser "${active_deploy_env_property_file}" | cut -d '=' -f 2`;
			echo "$applicationuser";

			applicationpasswd=`grep applicationpasswd "${active_deploy_env_property_file}" | cut -d '=' -f 2`;
			echo "$applicationpasswd";

			temp_properties=tmp/temp.properties;
		 	echo "$temp_properties";
		 	#created during setup-test/prod-package;
		 	package_name=`grep package.name $temp_properties | cut -d '=' -f 2`;
		 	echo "$package_name";

			branch_name=`grep 'git_branch' "${branch_to_build_property_file}" | cut -d '=' -f 2`;
			echo "$branch_name";

			echo "==========installation logs==========";
			echo "${installation_logs}";

			echo "==========basename==========";
			cd "${installation_logs}"/"${branch_name}"/"${package_name}"/binaries/;
			basenameforjar=`basename *.jar`;
			echo "${installation_logs}""\\""${branch_name}""\\""${package_name}""\\"binaries"\\""${basenameforjar}";
			jaruplpath1=`echo "${installation_logs}""\\\""${branch_name}""\\\""${package_name}""\\\"binaries"\\\""${basenameforjar}"`;
			echo "$jaruplpath1";

			echo "==========realname==========";			
			cd "${installation_logs}"/"${branch_name}"/"${package_name}"/binaries/;
			realpath=`realpath *.jar`;
			echo "$realpath";

			first=`echo "$realpath" | cut -c2- | cut -c1 | tr [a-z] [A-Z]`;
			second=`echo "$realpath" | cut -c3-`;

			echo "==========first+second+sed==========";	
			jaruplpath2=`echo $first$second | sed -e "s/\(.\)/\1:/"`;
			echo "$jaruplpath2";	


			cd "${robot}"

			robot -T -d reports -v url:"${applicationurl}" -v user:"${applicationuser}" -v password:"${applicationpasswd}" -v uplpath:"${jaruplpath}" ConfiguratorJARUpload.robot; 

robot -T -d reports -v url:http://sehan9507ca.han.telia.se:8077/OA_HTML/AppsLogin -v user:sysadmin -v password:admina2uat -v uplpath:D:\\Documents\\Alex\\A2\\Alpha2_Build_Infra\\cm_scripts\\logs\\ALPHA2-1.7.25\\ALPHA2-1.7.25-BLD1\\binaries\\configurator2019-02-23_1503.jar ConfiguratorJARUpload.robot

		cd /d/Documents/Alex/A2/Alpha2_Build_Infra/cm_scripts/robot/ConfiguratorJARUpload/-->
		<echo message=""/>
		<echo message="B_End: ${clock}"/>	
		<echo message=""/>		
		<echo message="========================================================= JAR_File_Upload ========================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>


	<target name="JAR_File_Upload_temp">
		<echo message="==================================================================================================================================="/>
		<echo message="========================================================= JAR_File_Upload ========================================================="/>
		<echo message=""/>
		<echo message="B_Start: ${clock}"/>	
		<echo message=""/>		

		<shellscript shell="bash">

			applicationurl=`grep applicationurl "${active_deploy_env_property_file}" | cut -d '=' -f 2`;
			echo "$applicationurl";
			applicationurl=`echo "$applicationurl" | tr -d '\\'`;
			echo "$applicationurl";

			applicationuser=`grep applicationuser "${active_deploy_env_property_file}" | cut -d '=' -f 2`;
			echo "$applicationuser";

			applicationpasswd=`grep applicationpasswd "${active_deploy_env_property_file}" | cut -d '=' -f 2`;
			echo "$applicationpasswd";

			temp_properties=tmp/temp.properties;
		 	echo "$temp_properties";
		 	#created during setup-test/prod-package;
		 	package_name=`grep package.name $temp_properties | cut -d '=' -f 2`;
		 	echo "$package_name";

			branch_name=`grep 'git_branch' "${branch_to_build_property_file}" | cut -d '=' -f 2`;
			echo "$branch_name";

			echo "==========basename==========";
			cd "${installation_logs}"/"${branch_name}"/"${package_name}"/binaries/;
			basenameforjar=`basename *.jar`;
			jaruplpath1=`echo "${installation_logs}""\\\""${branch_name}""\\\""${package_name}""\\\"binaries"\\\""${basenameforjar}"`;
			echo "$jaruplpath1";

			echo "==========realname==========";			
			cd "${installation_logs}"/"${branch_name}"/"${package_name}"/binaries/;
			realpath=`realpath *.jar`;

			first=`echo "$realpath" | cut -c2- | cut -c1 | tr [a-z] [A-Z]`;
			second=`echo "$realpath" | cut -c3-`;

			jaruplpath2=`echo $first$second | sed -e "s/\(.\)/\1:/"`;
			echo "$jaruplpath2";

			cd "${robot}";

			robot -T -d reports -v url:"${applicationurl}" -v user:"${applicationuser}" -v password:"${applicationpasswd}" -v uplpath:"${jaruplpath1}" ConfiguratorJARUpload.robot; 
			
		</shellscript>	

		<echo message=""/>
		<echo message="B_End: ${clock}"/>	
		<echo message=""/>		
		<echo message="========================================================= JAR_File_Upload ========================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>	

<!-- End of targets to install jar patch -->

<!-- Start of targets to install application patch -->

	<target name="app_binary_unpack" depends="clock">
		<echo message="==================================================================================================================================="/>
		<echo message="======================================================= app binary unpack ========================================================="/>
		<echo message=""/>
		<echo message="B_Start: ${clock}"/>
		<echo message=""/>


		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>		


 		<sshexec host="${aphost}" username="${apuser}" password="${appasswd}" command="source .bash_profile; echo &#x00022;Start:&#x00022;;date; cd CEB_PACK; sh ceb_install ${package_name} ${appspasswd} ${schemapasswd} U; echo &#x00022;End&#x00022;; date" trust="true" output="${installation_logs}/${branch_name}/${package_name}/${host_name}/app_binary_unpack.log" append="false" failonerror="false" verbose="false" timeout="10000" inputstring="R"/>

		<echo message=""/> 		
 		<echo message="B_End: ${clock}"/>
		<echo message=""/> 		
		<echo message="======================================================= app binary unpack ========================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>

	<target name="app_binary_install" depends="clock">
		<echo message="==================================================================================================================================="/>
		<echo message="======================================================= app binary install ========================================================"/>
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>
		<echo message=""/>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>		

		<antcall target="validate_objects"/>
		<antcall target="check_invalid_objects">
			<param name="installation_state" value="BEFORE_INSTALLATION"/>
		</antcall>		

 		<sshexec host="${aphost}" username="${apuser}" password="${appasswd}" command="source .bash_profile; . apache-tomcat/bin/setenv.sh; echo &#x00022;start&#x00022;; date; cd CEB_PACK; nohup sh ceb_install ${package_name} ${appspasswd} ${schemapasswd} BIRC &amp; &gt;&gt; nohup.out 2&gt;&amp;1 &amp;" trust="true" output="${installation_logs}/${branch_name}/${package_name}/${host_name}/app_binary_install.log" append="true" failonerror="false" verbose="false" timeout="10000" inputstring="R"/>

		<antcall target="check_status">
			<param name="module_to_check_status" value="app_binary_install_status"/>
			<param name="status_to_check" value="Compiling finished"/>
			<param name="time_frequency_to_check_status" value="3m"/>
			<param name="count_frequency_to_check_status" value="15"/>
			<param name="brch_name" value="${branch_name}"/>							
			<param name="pkg_name" value="${package_name}"/>		
			<param name="hst_name" value="${host_name}"/>							
			<param name="property_to_set" value="app_binary_install_complete"/>
		</antcall>

		<var name="app_binary_install_complete" unset="true"/>
		<loadproperties srcFile="${installation_status_property_file}"/>		
		<propertycopy name="app_binary_install_complete" from="app_binary_install_complete"/>
		<echo message="app_binary_install_complete: ${app_binary_install_complete}"/>

		<if>
			<equals arg1="${app_binary_install_complete}" arg2="0"/>
				<then>
					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="============================= app binary install complete ================================"/>
					<echo message="===================================== ! Info ! ==========================================="/>
				</then>
		<else>
				<echo message="===================================== ! Alert ! =========================================="/>			
				<echo message="========== app binary install not complete yet. Recommend manual invtervention ==========="/>
				<echo message="===================================== ! Alert ! =========================================="/>				
		</else>
		</if>

		<antcall target="validate_objects"/>

		<antcall target="check_invalid_objects">
			<param name="installation_state" value="AFTER_INSTALLATION"/>
		</antcall>					

		<echo message=""/>
 		<echo message="B_End: ${clock}"/>
		<echo message=""/> 		
		<echo message="======================================================= app binary install ========================================================"/>
		<echo message="==================================================================================================================================="/>
	</target>

	<!--; echo &#x00022;End:&#x00022;; date  sh ceb_install ALPHA2-1.3.3_master_28092018 appla2mx sehan9507caA2MX BIRC >> nohup.out 2>&1 &-->

	<target name="check_app_binary_install_status" depends="clock,scp_app_binary_inst_log">
		<echo message="==================================================================================================================================="/>
		<echo message="================================================== app binary install status ======================================================"/>
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>			
		<echo message=""/>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>


		<loadfile property="ceb_pack_installation_log" srcfile="${installation_logs}/${branch_name}/${package_name}/${host_name}/ceb_pack_${package_name}.log">  

			<filterchain>
				<tokenfilter>
   					<containsstring contains="Compiling finished"/>
				</tokenfilter>
			</filterchain>
 		</loadfile>

 		<condition property="ceb_pack_installation_complete" value="true" else="false">
 			<isset property="ceb_pack_installation_log"/>
 		</condition>

		<switch value="${ceb_pack_installation_complete}">
			<case value="true">
				<echo message="ceb_pack_installation_complete"/>
 				<propertyfile file="Flags/Flags.properties">
 					<entry key="ceb_pack_installation_complete" value="true"/>
 				</propertyfile>	
			</case>
			<case value="false">
 				<echo message="ceb_pack_installation_is_not_complete"/>
 				<propertyfile file="Flags/Flags.properties">
 					<entry key="ceb_pack_installation_complete" value="false"/>
 				</propertyfile>	
			</case>
		</switch>

		<echo message=""/>		
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="================================================== app binary install status ======================================================"/>
		<echo message="==================================================================================================================================="/>
	</target>	 				

<!-- End of targets to install application patch -->

<!-- find . -type f -print0 | xargs -0 -n 1 -P 4 dos2unix; -->

<!-- Start of targets to install db -->

	<target name="db_binary_unpack" depends="clock">
		<echo message="==================================================================================================================================="/>
		<echo message="======================================================== db_binary_unpack ========================================================="/>
		<echo message=""/>
		<echo message="B_Start: ${clock}"/>
		<echo message=""/>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>


		<sshexec host="${dbhost}" username="${dbuser}" password="${dbpasswd}" command="source .bash_profile; echo &#x00022;Start:&#x00022;;date; cd $HOME/CEBDB_PACK/; tar -xzvf $HOME/CEBDB_PACK/xxfi_db.tar.gz; cd $HOME/CEBDB_PACK/xxfi_db/ceb_scripts/; tar -xzvf $HOME/CEBDB_PACK/xxfi_db/ceb_scripts/apache-ant-1.6.5.tar.gz; cat $ORACLE_HOME/bin/sqlplus_wrapper; chmod -R 777 $HOME/CEBDB_PACK/; echo &#x00022;End&#x00022;; date" trust="true" output="${installation_logs}/db_installation/${branch_name}/${host_name}/db_binary_unpack.log" append="false" failonerror="false" verbose="false" timeout="1200000" inputstring="R"/>

		<!-- aaa -->

		<echo message=""/> 		
 		<echo message="B_End: ${clock}"/>
		<echo message=""/> 		
		<echo message="======================================================== db_binary_unpack ========================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>

	<target name="db_install" depends="clock">
		<echo message="==================================================================================================================================="/>		
		<echo message="========================================================= db_install =============================================================="/>
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>
		<echo message=""/>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>

 		<sshexec host="${dbhost}" username="${dbuser}" password="${dbpasswd}" command="source .bash_profile; export JAVA_HOME=$ORACLE_HOME/jdk; export JAVA_TOOL_OPTIONS=-Dfile.encoding=iso-8859-1; export ANT_HOME=$HOME/CEBDB_PACK/xxfi_db/ceb_scripts/apache-ant-1.6.5; export ANT_BIN=$HOME/CEBDB_PACK/xxfi_db/ceb_scripts/apache-ant-1.6.5/bin/ant; echo $HOME; echo $ORACLE_HOME; echo $JAVA_HOME; echo $JAVA_TOOL_OPTIONS; echo $ANT_HOME; echo $ANT_BIN; cd $HOME/CEBDB_PACK/xxfi_db; $ANT_BIN -D oracle_sid=${dbsid} -D apps_password=${appspasswd} -D schema_password=${schemapasswd} -logfile log.log install; echo &#x00022;start&#x00022;; date;" trust="true" output="${installation_logs}/db_installation/${branch_name}/${host_name}/db_install.log" append="true" failonerror="false" verbose="false" timeout="1200000" inputstring="R"/>
 		
		<antcall target="check_status">
			<param name="module_to_check_status" value="db_install_status"/>		
			<param name="status_to_check" value="BUILD SUCCESSFUL"/>			
			<param name="time_frequency_to_check_status" value="2m"/>			
			<param name="count_frequency_to_check_status" value="10"/>			
			<param name="brch_name" value="${branch_name}"/>							
			<!--param name="pkg_name" value="${db_package_name}"/-->		
			<param name="hst_name" value="${host_name}"/>			
			<param name="property_to_set" value="db_install_complete"/>
		</antcall>

		<var name="db_install_complete" unset="true"/>

		<loadproperties srcFile="${installation_status_property_file}"/>		
		<propertycopy name="db_install_complete" from="db_install_complete"/>
		<echo message="db_install_complete: ${db_install_complete}"/>

		<if>
			<equals arg1="${db_install_complete}" arg2="0"/>
				<then>
					<echo message="===================================== ! Info ! ==========================================="/>					
					<echo message="================================= db install complete ===================================="/>
					<echo message="===================================== ! Info ! ==========================================="/>
				</then>
		<else>
				<echo message="===================================== ! Alert ! =========================================="/>
				<echo message="============= db install not complete yet. Recommend manual invtervention ================"/>
				<echo message="===================================== ! Alert ! =========================================="/>
		</else>
		</if>

		<echo message=""/>
 		<echo message="B_End: ${clock}"/>
		<echo message=""/> 		
		<echo message="========================================================= db_install =============================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>

	<target name="check_db_install_status" depends="clock,scp_db_inst_log">
		<echo message="==================================================================================================================================="/>
		<echo message="======================================================= db_install_status ========================================================="/>
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>			
		<echo message=""/>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>

		<!-- aaa -->

		<loadfile property="db_installation_log" srcfile="${installation_logs}/db_installation/${branch_name}/${host_name}/log.log">	
			<filterchain>
 					<tokenfilter>
    					<containsstring contains="BUILD SUCCESSFUL"/>
					</tokenfilter>
 				</filterchain>
 		</loadfile>

 		<condition property="db_installation_complete" value="true" else="false">
 			<isset property="db_installation_log"/>
 		</condition>

		<switch value="${db_installation_complete}">
			<case value="true">
				<echo message="db_installation_complete"/>
 				<propertyfile file="Flags/Flags.properties">
 					<entry key="db_installation_complete" value="true"/>
 				</propertyfile>	
			</case>
			<case value="false">
 				<echo message="db_installation_is_not_complete"/>
 				<propertyfile file="Flags/Flags.properties">
 					<entry key="db_installation_complete" value="false"/>
 				</propertyfile>	
			</case>
		</switch>

		<echo message=""/>		
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="======================================================= db_install_status ========================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>	 				

<!-- End of targets to install db -->

<!-- Start of targets to install db_patch -->

	<target name="db_patch_binary_unpack" depends="clock">
		<echo message="==================================================================================================================================="/>
		<echo message="==================================================== db_patch_binary_unpack ======================================================="/>
		<echo message=""/>
		<echo message="B_Start: ${clock}"/>
		<echo message=""/>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>	
		<loadproperties srcFile="${db_build_property_file}"/>
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="db_package_name" from="db.package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>

		<sshexec host="${dbhost}" username="${dbuser}" password="${dbpasswd}" command="source .bash_profile; echo &#x00022;Start:&#x00022;;date; mkdir $HOME/CEBDB_PACK/log; chmod -R 777 $HOME/CEBDB_PACK/log/; cd $HOME/CEBDB_PACK/xxfi_db/ceb_scripts/; sh cebdb_install ${db_package_name} ${appspasswd} ${schemapasswd} U; chmod -R 777 $HOME/CEBDB_PACK; echo &#x00022;End&#x00022;; date" trust="true" output="${installation_logs}/${branch_name}/${package_name}/${db_package_name}/${host_name}/db_patch_binary_unpack.log" append="false" failonerror="false" verbose="false" timeout="1200000" inputstring="R"/>

		<echo message=""/> 		
 		<echo message="B_End: ${clock}"/>
		<echo message=""/> 		
		<echo message="==================================================== db_patch_binary_unpack ======================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>

	<target name="db_patch_binary_install" depends="clock">
		<echo message="==================================================================================================================================="/>
		<echo message="=================================================== db_patch_binary_install ======================================================="/>
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>
		<echo message=""/>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>
		<loadproperties srcFile="${db_build_property_file}"/>
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="db_package_name" from="db.package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>

		<antcall target="validate_objects"/>
		<antcall target="check_invalid_objects">
			<param name="installation_state" value="BEFORE_DB-PATCH_INSTALL"/>
		</antcall>				

		<sshexec host="${dbhost}" username="${dbuser}" password="${dbpasswd}" command="source .bash_profile; echo &#x00022;Start:&#x00022;;date; cd $HOME/CEBDB_PACK/xxfi_db/ceb_scripts/; sh cebdb_install ${db_package_name} ${appspasswd} ${schemapasswd} I; echo &#x00022;End&#x00022;; date" trust="true" output="${installation_logs}/${branch_name}/${package_name}/${db_package_name}/${host_name}/db_patch_binary_install.log" append="false" failonerror="false" verbose="false" timeout="1200000" inputstring="R"/>

		<antcall target="check_status">
			<param name="module_to_check_status" value="db_patch_binary_install_status"/>		
			<param name="status_to_check" value="BUILD SUCCESSFUL"/>					
			<param name="time_frequency_to_check_status" value="3m"/>
			<param name="count_frequency_to_check_status" value="15"/>			
			<param name="brch_name" value="${branch_name}"/>		
			<param name="pkg_name" value="${package_name}/${db_package_name}"/>		
			<param name="hst_name" value="${host_name}"/>					
			<param name="property_to_set" value="db_patch_binary_install_complete"/>
		</antcall>

		<var name="db_patch_binary_install_complete" unset="true"/>

		<loadproperties srcFile="${installation_status_property_file}"/>		
		<propertycopy name="db_patch_binary_install_complete" from="db_patch_binary_install_complete"/>
		<echo message="db_patch_binary_install_complete: ${db_patch_binary_install_complete}"/>

		<if>
			<equals arg1="${db_patch_binary_install_complete}" arg2="0"/>
				<then>
					<echo message="===================================== ! Info ! ==========================================="/>					
					<echo message="========================== db patch binary install complete =============================="/>
					<echo message="===================================== ! Info ! ==========================================="/>
				</then>
		<else>
				<echo message="===================================== ! Alert ! =========================================="/>			
				<echo message="======== db patch binary install not complete yet. Recommend manual invtervention ========"/>
				<echo message="===================================== ! Alert ! =========================================="/>
		</else>
		</if>

		<antcall target="validate_objects"/>
		<antcall target="check_invalid_objects">
			<param name="installation_state" value="AFTER_DB-PATCH_INSTALL"/>
		</antcall>	

		<echo message=""/>
 		<echo message="B_End: ${clock}"/>
		<echo message=""/> 		
		<echo message="=================================================== db_patch_binary_install ======================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>

	<target name="check_db_patch_binary_install_status" depends="clock,scp_db_binary_inst_log">
		<echo message="==================================================================================================================================="/>
		<echo message="================================================ db_patch_binary_install_status ==================================================="/>
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>			
		<echo message=""/>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<propertycopy name="package_name" from="package.name"/>
		<loadproperties srcFile="${db_build_property_file}"/>
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="db_package_name" from="db.package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>

		<loadfile property="cebdb_pack_installation_log" srcfile="${installation_logs}/${branch_name}/${package_name}/${db_package_name}/${host_name}/cebdb_pack_${db_package_name}.log">  
			<filterchain>
 					<tokenfilter>
    					<containsstring contains="BUILD SUCCESSFUL"/>
					</tokenfilter>
 				</filterchain>
 		</loadfile>

 		<condition property="cebdb_pack_installation_complete" value="true" else="false">
 			<isset property="cebdb_pack_installation_log"/>
 		</condition>

		<switch value="${cebdb_pack_installation_complete}">
			<case value="true">
				<echo message="cebdb_pack_installation_complete"/>
 				<propertyfile file="Flags/Flags.properties">
 					<entry key="cebdb_pack_installation_complete" value="true"/>
 				</propertyfile>	
			</case>
			<case value="false">
 				<echo message="cebdb_pack_installation_is_not_complete"/>
 				<propertyfile file="Flags/Flags.properties">
 					<entry key="cebdb_pack_installation_complete" value="false"/>
 				</propertyfile>	
			</case>
		</switch>

		<echo message=""/>		
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="================================================ db_patch_binary_install_status ==================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>	 				

<!-- End of targets to install db_patch -->

<!-- Start of targets to manage ebs_web -->

	<target name="ebs_web_shutdown" depends="clock,check_ebs_web_status">
		<echo message="==================================================================================================================================="/>
		<echo message="====================================================== ebs_web_shutdown ==========================================================="/>
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>			
		<echo message=""/>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>


		<if>
			<equals arg1="${ebs_web_status}" arg2="Alive"/>
				<then>
					<sshexec host="${aphost}" username="${apuser}" password="${appasswd}" command="source .bash_profile run; echo &#x00022;Start:&#x00022;;date; adapcctl.sh stop; echo &#x00022;End&#x00022;; date" trust="true" output="${installation_logs}/${branch_name}/${package_name}/${host_name}/ebs_web_shutdown.log" append="false" failonerror="false" verbose="false" timeout="60000000"/>
				</then>
			<else>
				<echo message="ebs_web is ${ebs_web_status}"/>
			</else>
		</if>

		<echo message=""/>		
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="====================================================== ebs_web_shutdown ==========================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>

	<target name="ebs_web_start" depends="clock,check_ebs_web_status">
		<echo message="==================================================================================================================================="/>
		<echo message="====================================================== ebs_web_start =============================================================="/>
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>			
		<echo message=""/>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>

		<if>
			<equals arg1="${ebs_web_status}" arg2="Down"/>
				<then>
					<sshexec host="${aphost}" username="${apuser}" password="${appasswd}" command="source .bash_profile run; echo &#x00022;Start:&#x00022;;date; adapcctl.sh start; echo &#x00022;End&#x00022;; date" trust="true" output="${installation_logs}/${branch_name}/${package_name}/${host_name}/ebs_web_start.log" append="false" failonerror="false" verbose="false" timeout="60000000"/>
				</then>
			<else>
				<echo message="ebs_web is ${ebs_web_status}"/>
			</else>
		</if>

		<echo message=""/>		
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="====================================================== ebs_web_start =============================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>		

	<target name="check_ebs_web_status" depends="clock">
		<echo message="==================================================================================================================================="/>
		<echo message="=================================================== check_ebs_web_status =========================================================="/>
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>			
		<echo message=""/>


		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>		


 		<sshexec host="${aphost}" username="${apuser}" password="${appasswd}" command="source .bash_profile run; echo &#x00022;Start:&#x00022;;date; adapcctl.sh status; echo &#x00022;End&#x00022;; date" trust="true" output="${installation_logs}/${branch_name}/${package_name}/${host_name}/ebs_web_status.log" append="false" failonerror="false" verbose="false" timeout="20000"/>
 		<loadfile property="file" srcfile="${installation_logs}/${branch_name}/${package_name}/${host_name}/ebs_web_status.log"/>
 		<propertyregex property="ebs_web_status" input="${file}" regexp="^(?:[^|]*\|){6}\s([^\v]*)(\b)" select="\1" override="true"/>
 		<echo message="ebs_web is ${ebs_web_status}"/>
 		<propertyfile file="Flags/Flags.properties">
 			<entry key="ebs_web_status" value="${ebs_web_status}"/>
 		</propertyfile>

		<echo message=""/> 		
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="=================================================== check_ebs_web_status =========================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>		

<!-- End of targets to manage ebs_web -->

<!-- Start of targets to manage oacore -->

	<target name="oacore_shutdown" depends="clock,check_oacore_status">
		<echo message="==================================================================================================================================="/>
		<echo message="======================================================= oacore_shutdown ==========================================================="/>
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>			
		<echo message=""/>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>

		<if>
			<equals arg1="${oacore_status}" arg2="running"/>
				<then>
					<sshexec host="${aphost}" username="${apuser}" password="${appasswd}" command="source .bash_profile run; echo &#x00022;Start:&#x00022;;date; admanagedsrvctl.sh stop oacore_server1; echo &#x00022;End&#x00022;; date" trust="true" output="${installation_logs}/${branch_name}/${package_name}/${host_name}/oacore_shutdown.log" append="false" failonerror="false" verbose="false" timeout="60000000" inputstring="${weblogicpasswd}"/>
				</then>
			<else>
				<echo message="oacore is ${oacore_status}"/>
			</else>
		</if>

		<echo message=""/>		
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="======================================================= oacore_shutdown ==========================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>

	<target name="oacore_start" depends="clock,check_oacore_status">
		<echo message="==================================================================================================================================="/>
		<echo message="======================================================== oacore_start ============================================================="/>
		<echo message=""/>				
		<echo message="B_Start: ${clock}"/>			
		<echo message=""/>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>

		<if>
			<equals arg1="${oacore_status}" arg2="shutdown"/>
				<then>
					<sshexec host="${aphost}" username="${apuser}" password="${appasswd}" command="source .bash_profile run; echo &#x00022;Start:&#x00022;;date; admanagedsrvctl.sh start oacore_server1; echo &#x00022;End&#x00022;; date" trust="true" output="${installation_logs}/${branch_name}/${package_name}/${host_name}/oacore_start.log" append="false" failonerror="false" verbose="false" timeout="60000000" inputstring="${weblogicpasswd}"/>
				</then>
			<else>
				<echo message="oacore is ${oacore_status}"/>
			</else>
		</if>

		<echo message=""/>		
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="======================================================== oacore_start ============================================================="/>		
		<echo message="==================================================================================================================================="/>
	</target>		

	<target name="check_oacore_status" depends="clock">
		<echo message="==================================================================================================================================="/>
		<echo message="======================================================== check_oacore_status ======================================================"/>
		<echo message=""/>
		<echo message="B_Start: ${clock}"/>			
		<echo message=""/>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>

 		<sshexec host="${aphost}" username="${apuser}" password="${appasswd}" command="source .bash_profile run; echo &#x00022;Start:&#x00022;;date; admanagedsrvctl.sh status oacore_server1; echo &#x00022;End&#x00022;; date" trust="true" output="${installation_logs}/${branch_name}/${package_name}/${host_name}/oacore_status.log" append="false" failonerror="false" verbose="false" timeout="6000000" inputstring="${weblogicpasswd}"/>
 		<loadfile property="file" srcfile="${installation_logs}/${branch_name}/${package_name}/${host_name}/oacore_status.log"/>
 		<propertyregex property="oacore_status" input="${file}" regexp="oacore_server1 is\s([^\v]*)(\b)" select="\1" override="true"/>
 		<propertyfile file="Flags/Flags.properties">
 			<entry key="oacore_status" value="${oacore_status}"/>
 		</propertyfile>

		<echo message=""/> 		
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="======================================================== check_oacore_status ======================================================"/>		
		<echo message="==================================================================================================================================="/>
	</target>		

<!-- End of targets to manage oacore -->

<!-- Start of targets to manage concurrent_manager -->

	<target name="concurrent_manager_shutdown" depends="clock,check_concurrent_manager_status">
		<echo message="==================================================================================================================================="/>
		<echo message="===================================================== concurrent_manager_shutdown ================================================="/>
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>			
		<echo message=""/>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>

		<if>
			<equals arg1="${concurrent_manager_status}" arg2="Active"/>
				<then>
					<sshexec host="${aphost}" username="${apuser}" password="${appasswd}" command="source .bash_profile; echo &#x00022;Start:&#x00022;;date; adcmctl.sh stop apps/${appspasswd}; echo &#x00022;End&#x00022;; date" trust="true" output="${installation_logs}/${branch_name}/${package_name}/${host_name}/concurrent_manager_shutdown.log" append="false" failonerror="false" verbose="false" timeout="300000" inputstring="R"/>
				</then>
			<else>
				<echo message="concurrent_manager is ${concurrent_manager_status}"/>
			</else>
		</if>

		<echo message=""/>		
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="===================================================== concurrent_manager_shutdown ================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>

		<target name="concurrent_manager_abort" depends="clock,check_concurrent_manager_status">
		<echo message="==================================================================================================================================="/>
		<echo message="======================================================= concurrent_manager_abort =================================================="/>
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>			
		<echo message=""/>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>

		<if>
			<equals arg1="${concurrent_manager_status}" arg2="Active"/>
				<then>
					<sshexec host="${aphost}" username="${apuser}" password="${appasswd}" command="source .bash_profile; echo &#x00022;Start:&#x00022;;date; adcmctl.sh abort apps/${appspasswd}; echo &#x00022;End&#x00022;; date" trust="true" output="${installation_logs}/${branch_name}/${package_name}/${host_name}/concurrent_manager_shutdown.log" append="false" failonerror="false" verbose="false" timeout="300000" inputstring="R"/>
				</then>
			<else>
				<echo message="concurrent_manager is ${concurrent_manager_status}"/>
			</else>
		</if>

		<echo message=""/>
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="======================================================= concurrent_manager_abort =================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>

	<target name="concurrent_manager_start" depends="clock,check_concurrent_manager_status">
		<echo message="==================================================================================================================================="/>
		<echo message="======================================================= concurrent_manager_start =================================================="/>
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>			
		<echo message=""/>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>

		<if>
			<equals arg1="${concurrent_manager_status}" arg2="Not Active at this time"/>
				<then>
					<sshexec host="${aphost}" username="${apuser}" password="${appasswd}" command="source .bash_profile; echo &#x00022;Start:&#x00022;;date; adcmctl.sh start apps/${appspasswd}; echo &#x00022;End&#x00022;; date" trust="true" output="${installation_logs}/${branch_name}/${package_name}/${host_name}/concurrent_manager_start.log" append="false" failonerror="false" verbose="false" timeout="20000" inputstring="R"/>
				</then>
			<else>
				<echo message="concurrent_manager is ${concurrent_manager_status}"/>
			</else>
		</if>

		<echo message=""/>		
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="======================================================= concurrent_manager_start =================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>		

	<target name="check_concurrent_manager_status" depends="clock">
		<echo message="==================================================================================================================================="/>
		<echo message="=============================================== check_concurrent_manager_status ==================================================="/>
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>
		<echo message=""/>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>

 		<sshexec host="${aphost}" username="${apuser}" password="${appasswd}" command="source .bash_profile; echo &#x00022;Start:&#x00022;;date; adcmctl.sh status apps/${appspasswd}; echo &#x00022;End&#x00022;; date" trust="true" output="${installation_logs}/${branch_name}/${package_name}/${host_name}/concurrent_manager_status.log" append="false" failonerror="false" verbose="false" timeout="20000" inputstring="R"/>
 		<loadfile property="file" srcfile="${installation_logs}/${branch_name}/${package_name}/${host_name}/concurrent_manager_status.log"/>
 		<propertyregex property="concurrent_manager_status" input="${file}" regexp="Internal Concurrent Manager is\s([^\v]*)(\b)" select="\1" override="true"/>
 		<echo message="concurrent_manager is ${concurrent_manager_status}"/>
 		<propertyfile file="Flags/Flags.properties">
 			<entry key="concurrent_manager_status" value="${concurrent_manager_status}"/>
 		</propertyfile>

		<echo message=""/> 		
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="=============================================== check_concurrent_manager_status ==================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>		

<!-- End of targets to manage concurrent_manager -->

<!-- Start of targets to manage tomcat -->

	<target name="tomcat_restart" depends="clock">
		<echo message="==================================================================================================================================="/>
		<echo message="======================================================== tomcat_restart ==========================================================="/>
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>			
		<echo message=""/>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>	

  		<antcall target="tomcat_shutdown"/>
  		<!--antcall target="check_tomcat_status"/-->

		<antcall target="check_status">
			<param name="module_to_check_status" value="tomcat_status"/>
			<param name="status_to_check" value="DOWN"/>
			<param name="time_frequency_to_check_status" value="30s"/>
			<param name="count_frequency_to_check_status" value="2"/>
			<param name="brch_name" value="${branch_name}"/>							
			<param name="pkg_name" value="${package_name}"/>		
			<param name="hst_name" value="${host_name}"/>							
			<param name="property_to_set" value="tomcat_down"/>
		</antcall>

		<var name="tomcat_down" unset="true"/>
		<loadproperties srcFile="${installation_status_property_file}"/>		
		<propertycopy name="tomcat_down" from="tomcat_down"/>
		<echo message="tomcat_down: ${tomcat_down}"/>

		<if>
			<equals arg1="${tomcat_down}" arg2="0"/>
				<then>
					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="=================================== tomcat down =========================================="/>
					<echo message="===================================== ! Info ! ==========================================="/>
				</then>
		<else>
				<echo message="===================================== ! Alert ! =========================================="/>			
				<echo message="================= tomcat not down yet. Recommend manual invtervention ===================="/>
				<echo message="===================================== ! Alert ! =========================================="/>				
		</else>
		</if>

  		<antcall target="tomcat_start"/>
  		<!--antcall target="check_tomcat_status"/-->  		   				


		<antcall target="check_status">
			<param name="module_to_check_status" value="tomcat_status"/>
			<param name="status_to_check" value="UP"/>
			<param name="time_frequency_to_check_status" value="30s"/>
			<param name="count_frequency_to_check_status" value="2"/>
			<param name="brch_name" value="${branch_name}"/>							
			<param name="pkg_name" value="${package_name}"/>		
			<param name="hst_name" value="${host_name}"/>							
			<param name="property_to_set" value="tomcat_up"/>
		</antcall>

		<var name="tomcat_up" unset="true"/>
		<loadproperties srcFile="${installation_status_property_file}"/>		
		<propertycopy name="tomcat_up" from="tomcat_up"/>
		<echo message="tomcat_up: ${tomcat_up}"/>

		<if>
			<equals arg1="${tomcat_up}" arg2="0"/>
				<then>
					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="===================================== tomcat up =========================================="/>
					<echo message="===================================== ! Info ! ==========================================="/>
				</then>
		<else>
				<echo message="===================================== ! Alert ! =========================================="/>			
				<echo message="================== tomcat not up yet. Recommend manual invtervention ====================="/>
				<echo message="===================================== ! Alert ! =========================================="/>				
		</else>
		</if>

  		
		<echo message=""/> 			
 		<echo message="B_End: ${clock}"/>	
		<echo message=""/> 		
		<echo message="======================================================== tomcat_restart ==========================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>

	<target name="tomcat_shutdown" depends="clock">
		<echo message="==================================================================================================================================="/>
		<echo message="======================================================== tomcat_shutdown =========================================================="/>
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>			
		<echo message=""/>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>


 		<condition property="tomcat_status" value="UP" else="DOWN">
 			<socket server="${aphost}" port="${tomcatport}"/>
 		</condition>
		<if>
			<equals arg1="${tomcat_status}" arg2="UP"/>
				<then>
					<sshexec host="${aphost}" username="${apuser}" password="${appasswd}" command="source .bash_profile run; echo &#x00022;Start:&#x00022;;date; . apache-tomcat/bin/setenv.sh; echo $CATALINA_HOME; $CATALINA_HOME/bin/shutdown.sh; echo &#x00022;End&#x00022;; date" trust="true" output="${installation_logs}/${branch_name}/${package_name}/${host_name}/tomcat_shutdown.log" append="false" failonerror="false" verbose="false" timeout="20000"/>
				</then>
			<elseif>
				<equals arg1="${tomcat_status}" arg2="DOWN"/>
					<then>
						<echo message="Tomcat is ${tomcat_status}"/>
					</then>
			</elseif>
		</if>

		<echo message=""/>		
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="======================================================== tomcat_shutdown =========================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>

	<target name="tomcat_start" depends="clock">		
		<echo message="==================================================================================================================================="/>
		<echo message="========================================================== tomcat_start ==========================================================="/>
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>			
		<echo message=""/>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>

 		<condition property="tomcat_status" value="UP" else="DOWN">
 			<socket server="${aphost}" port="${tomcatport}"/>
 		</condition>
		<if>
			<equals arg1="${tomcat_status}" arg2="DOWN"/>
				<then>
					<sshexec host="${aphost}" username="${apuser}" password="${appasswd}" command="source .bash_profile run; echo &#x00022;Start:&#x00022;;date; . apache-tomcat/bin/setenv.sh; echo $CATALINA_HOME; $CATALINA_HOME/bin/startup.sh; echo &#x00022;End&#x00022;; date" trust="true" output="${installation_logs}/${branch_name}/${package_name}/${host_name}/tomcat_start.log" append="false" failonerror="false" verbose="false" timeout="20000"/>
				</then>
			<elseif>
				<equals arg1="${tomcat_status}" arg2="UP"/>
					<then>
						<echo message="Tomcat is ${tomcat_status}"/>
					</then>
			</elseif>
		</if>

		<echo message=""/>		
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="========================================================== tomcat_start ==========================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>

	<target name="check_tomcat_status" depends="clock">
		<echo message="==================================================================================================================================="/>
		<echo message="======================================================== check_tomcat_status ======================================================"/>
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>			
		<echo message=""/>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>

 		<condition property="tomcat_status" value="UP" else="DOWN">
 			<socket server="${aphost}" port="${tomcatport}"/>
 		</condition>
 		
 		<echo message="Tomcat is ${tomcat_status}"/>

 		<echo file="${installation_logs}/${branch_name}/${package_name}/${host_name}/tomcat_status.log" append="false" message="Tomcat is ${tomcat_status}"/>
 		
 		<propertyfile file="Flags/Flags.properties">
 			<entry key="tomcat_status" value="${tomcat_status}"/>
 		</propertyfile>

		<echo message=""/> 		
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="======================================================== check_tomcat_status ======================================================"/>
		<echo message="==================================================================================================================================="/>
	</target>	

<!-- End of targets to manage tomcat -->

<!-- Start of targets for MD120 xxfi_i673 -->

	<target name="MD120_i673_install" depends="clock">
		<echo message="==================================================================================================================================="/>
		<echo message="===================================================== MD120_i673_install =========================================================="/>
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>			
		<echo message=""/>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>

		<!-- $XXFI_TOP/install/i673/xxfi_i673_setup_master.sh {apps password} {schema password} {mode} -->	
 		<sshexec host="${aphost}" username="${apuser}" password="${appasswd}" command="source .bash_profile run; echo &#x00022;Start:&#x00022;;date; . apache-tomcat/bin/setenv.sh; echo $CATALINA_HOME; $XXFI_TOP/install/i673/xxfi_i673_setup_master.sh ${appspasswd} ${schemapasswd} f; echo &#x00022;End:&#x00022;;date" trust="true" output="${installation_logs}/${branch_name}/${package_name}/${host_name}/MD120_i673_install.log" append="false" failonerror="false" verbose="false" timeout="120000"/>
 		<antcall target="check_MD120_i673_install_status"/>
  		<antcall target="tomcat_shutdown"/>
  		<antcall target="check_tomcat_status"/>
  		<antcall target="tomcat_start"/>
  		<antcall target="check_tomcat_status"/>  		   				
 		<antcall target="MD120_i673_deployAOS"/>
 		<antcall target="check_MD120_i673_deployAOS_status"/>
 		<antcall target="MD120_i673_deployAAS"/>
 		<antcall target="check_MD120_i673_deployAAS_status"/> 		
 		<antcall target="MD120_i673_deployAIS"/>
 		<antcall target="check_MD120_i673_deployAIS_status"/> 	

		<echo message=""/> 			
 		<echo message="B_End: ${clock}"/>	
		<echo message=""/> 		
		<echo message="===================================================== MD120_i673_install =========================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>

	<target name="check_MD120_i673_install_status" depends="clock">
		<echo message="==================================================================================================================================="/>
		<echo message="================================================== check_MD120_i673_install_status ================================================"/>
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>
		<echo message=""/>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>

 		<condition property="MD120_i673_install_status" value="complete" else="in progress">
 			<resourcecontains resource="${installation_logs}/${branch_name}/${package_name}/${host_name}/MD120_i673_install.log" substring="End Install Script xxfi_i673"></resourcecontains>
 		</condition>
 		<echo message="MD120_i673_install is ${MD120_i673_install_status}"/>
 		<propertyfile file="Flags/Flags.properties">
 			<entry key="MD120_i673_install_status" value="${MD120_i673_install_status}"/>
 		</propertyfile>

		<echo message=""/> 		
 		<echo message="B_End: ${clock}"/> 	
		<echo message=""/> 		
		<echo message="================================================== check_MD120_i673_install_status ================================================"/>
		<echo message="==================================================================================================================================="/>
	</target>		

	<target name="MD120_i673_deployAOS" depends="clock">
		<echo message="==================================================================================================================================="/>
		<echo message="==================================================== MD120_i673_deployAOS ========================================================="/>
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>
		<echo message=""/>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>		

 		<sshexec host="${aphost}" username="${apuser}" password="${appasswd}" command="source .bash_profile run; echo &#x00022;Start:&#x00022;;date; . apache-tomcat/bin/setenv.sh; echo $CATALINA_HOME; export CLASSPATH=$CLASSPATH:$CATALINA_HOME/webapps/axis/WEB-INF/lib/axis.jar:$CATALINA_HOME/webapps/axis/WEB-INF/lib/commons-discovery-0.2.jar:$CATALINA_HOME/webapps/axis/WEB-INF/lib/wsdl4j-1.5.1.jar:$CATALINA_HOME/webapps/axis/WEB-INF/lib/axis-1.4.jar:$CATALINA_HOME/webapps/axis/WEB-INF/lib/commons-discovery.jar:$CATALINA_HOME/webapps/axis/WEB-INF/lib/commons-logging-1.0.4.jar:$CATALINA_HOME/webapps/axis/WEB-INF/lib/jaxrpc.jar:$CATALINA_HOME/webapps/axis/WEB-INF/lib/log4j-1.2.8.jar:$CATALINA_HOME/webapps/axis/WEB-INF/lib/saaj.jar:$CATALINA_HOME/webapps/axis/WEB-INF/lib/wsdl4j.jar; cd $CATALINA_HOME/webapps/axis/AOS; java org.apache.axis.client.AdminClient -p ${tomcatport} deployAOS.wsdd; echo &#x00022;End&#x00022;; date" trust="true" output="${installation_logs}/${branch_name}/${package_name}/${host_name}/MD120_i673_deployAOS.log" append="false" failonerror="false" verbose="false" timeout="60000"/>

		<echo message=""/> 		
 		<echo message="B_End: ${clock}"/>
		<echo message=""/> 		
		<echo message="==================================================== MD120_i673_deployAOS ========================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>

	<target name="check_MD120_i673_deployAOS_status" depends="clock">
		<echo message="==================================================================================================================================="/>
		<echo message="=============================================== check_MD120_i673_deployAOS_status ================================================="/>
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>		
		<echo message=""/>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>

 		<loadfile property="file" srcfile="${installation_logs}/${branch_name}/${package_name}/${host_name}/MD120_i673_deployAOS.log"/>
 		<propertyregex property="deployAOS_status" input="${file}" regexp="\WAdmin xmlns=\W\W\W([^\_]*)\W\/Admin\W" select="\1" override="true"/>
 		<echo message="MD120_i673_deployAOS is ${deployAOS_status}"/>
 		<propertyfile file="Flags/Flags.properties">
 			<entry key="MD120_i673_deployAOS_status" value="${deployAOS_status}"/>
 		</propertyfile>

		<echo message=""/> 		
 		<echo message="B_End: ${clock}"/>
		<echo message=""/> 		
		<echo message="=============================================== check_MD120_i673_deployAOS_status ================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>	

	<target name="MD120_i673_deployAAS" depends="clock">
		<echo message="==================================================================================================================================="/>
		<echo message="==================================================== MD120_i673_deployAAS ========================================================="/>
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>		
		<echo message=""/>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>

 		<sshexec host="${aphost}" username="${apuser}" password="${appasswd}" command="source .bash_profile run; echo &#x00022;Start:&#x00022;;date; . apache-tomcat/bin/setenv.sh; echo $CATALINA_HOME; export CLASSPATH=$CLASSPATH:$CATALINA_HOME/webapps/axis/WEB-INF/lib/axis.jar:$CATALINA_HOME/webapps/axis/WEB-INF/lib/commons-discovery-0.2.jar:$CATALINA_HOME/webapps/axis/WEB-INF/lib/wsdl4j-1.5.1.jar:$CATALINA_HOME/webapps/axis/WEB-INF/lib/axis-1.4.jar:$CATALINA_HOME/webapps/axis/WEB-INF/lib/commons-discovery.jar:$CATALINA_HOME/webapps/axis/WEB-INF/lib/commons-logging-1.0.4.jar:$CATALINA_HOME/webapps/axis/WEB-INF/lib/jaxrpc.jar:$CATALINA_HOME/webapps/axis/WEB-INF/lib/log4j-1.2.8.jar:$CATALINA_HOME/webapps/axis/WEB-INF/lib/saaj.jar:$CATALINA_HOME/webapps/axis/WEB-INF/lib/wsdl4j.jar; cd $CATALINA_HOME/webapps/axis/AAS; java org.apache.axis.client.AdminClient -p ${tomcatport} deployAAS.wsdd; echo &#x00022;End&#x00022;; date" trust="true" output="${installation_logs}/${branch_name}/${package_name}/${host_name}/MD120_i673_deployAAS.log" append="false" failonerror="false" verbose="false" timeout="60000"/>

		<echo message=""/> 		
 		<echo message="B_End: ${clock}"/> 		
		<echo message=""/> 		
		<echo message="==================================================== MD120_i673_deployAAS ========================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>

	<target name="check_MD120_i673_deployAAS_status" depends="clock">
		<echo message="==================================================================================================================================="/>
		<echo message="=============================================== check_MD120_i673_deployAAS_status ================================================="/>
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>			
		<echo message=""/>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>

 		<loadfile property="file" srcfile="${installation_logs}/${branch_name}/${package_name}/${host_name}/MD120_i673_deployAAS.log"/>
 		<propertyregex property="deployAAS_status" input="${file}" regexp="\WAdmin xmlns=\W\W\W([^\_]*)\W\/Admin\W" select="\1" override="true"/>
 		<echo message="MD120_i673_deployAAS is ${deployAAS_status}"/>
 		<propertyfile file="Flags/Flags.properties">
 			<entry key="MD120_i673_deployAAS_status" value="${deployAAS_status}"/>
 		</propertyfile>

		<echo message=""/> 		
 		<echo message="B_End: ${clock}"/> 		
		<echo message=""/> 		
		<echo message="=============================================== check_MD120_i673_deployAAS_status ================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>	

	<target name="MD120_i673_deployAIS" depends="clock">
		<echo message="==================================================================================================================================="/>
		<echo message="==================================================== MD120_i673_deployAIS ========================================================="/>
		<echo message=""/>
		<echo message="B_Start: ${clock}"/>				
		<echo message=""/>				

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>

 		<sshexec host="${aphost}" username="${apuser}" password="${appasswd}" command="source .bash_profile run; echo &#x00022;Start:&#x00022;;date; . apache-tomcat/bin/setenv.sh; echo $CATALINA_HOME; export CLASSPATH=$CLASSPATH:$CATALINA_HOME/webapps/axis/WEB-INF/lib/axis.jar:$CATALINA_HOME/webapps/axis/WEB-INF/lib/commons-discovery-0.2.jar:$CATALINA_HOME/webapps/axis/WEB-INF/lib/wsdl4j-1.5.1.jar:$CATALINA_HOME/webapps/axis/WEB-INF/lib/axis-1.4.jar:$CATALINA_HOME/webapps/axis/WEB-INF/lib/commons-discovery.jar:$CATALINA_HOME/webapps/axis/WEB-INF/lib/commons-logging-1.0.4.jar:$CATALINA_HOME/webapps/axis/WEB-INF/lib/jaxrpc.jar:$CATALINA_HOME/webapps/axis/WEB-INF/lib/log4j-1.2.8.jar:$CATALINA_HOME/webapps/axis/WEB-INF/lib/saaj.jar:$CATALINA_HOME/webapps/axis/WEB-INF/lib/wsdl4j.jar; cd $CATALINA_HOME/webapps/axis/AIS; java org.apache.axis.client.AdminClient -p ${tomcatport} deployAIS.wsdd; echo &#x00022;End&#x00022;; date" trust="true" output="${installation_logs}/${branch_name}/${package_name}/${host_name}/MD120_i673_deployAIS.log" append="false" failonerror="false" verbose="false" timeout="60000"/>

		<echo message=""/>	 		
 		<echo message="B_End: ${clock}"/>
		<echo message=""/>	 		
		<echo message="==================================================== MD120_i673_deployAIS ========================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>

	<target name="check_MD120_i673_deployAIS_status" depends="clock">
		<echo message="==================================================================================================================================="/>
		<echo message="=============================================== check_MD120_i673_deployAIS_status ================================================="/>
		<echo message=""/>			
		<echo message="B_Start: ${clock}"/>		
		<echo message=""/>	

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>

 		<loadfile property="file" srcfile="${installation_logs}/${branch_name}/${package_name}/${host_name}/MD120_i673_deployAIS.log"/>
 		<propertyregex property="deployAIS_status" input="${file}" regexp="\WAdmin xmlns=\W\W\W([^\_]*)\W\/Admin\W" select="\1" override="true"/>
 		<echo message="MD120_i673_deployAIS is ${deployAIS_status}"/>
 		<propertyfile file="Flags/Flags.properties">
 			<entry key="MD120_i673_deployAIS_status" value="${deployAIS_status}"/>
 		</propertyfile>

		<echo message=""/>	 		
 		<echo message="B_End: ${clock}"/>
		<echo message=""/>	 		
		<echo message="=============================================== check_MD120_i673_deployAIS_status ================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>		

<!-- End of targets for MD120 xxfi_i673 -->

<!-- Start of targets for MD120 xxfe_f694 and xxfe_f695 -->

	<target name="MD120_f694_f695_install" depends="clock">		
		<echo message="==================================================================================================================================="/>
		<echo message="================================================== MD120_f694_f695_install ========================================================"/> 
		<echo message=""/>			
		<echo message="B_Start: ${clock}"/>		
		<echo message=""/>	

		<loadfile property="MD120_f694_f695_install_list" srcfile="${MD120_JSP_Installation_xxfe_f694_f695_file}"/>
		<foreach list="${MD120_f694_f695_install_list}" target="MD120_f694_f695_install_sub_process" param="lineitem" delimiter="${line.separator}" inheritall="true"/>

		<echo message=""/>			
		<echo message="B_End: ${clock}"/>
		<echo message=""/>			
		<echo message="================================================== MD120_f694_f695_install ========================================================"/> 
		<echo message="==================================================================================================================================="/>
	</target>

	<target name="MD120_f694_f695_install_sub_process">
		<echo message=""/>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>

 		<sshexec host="${aphost}" username="${apuser}" password="${appasswd}" command="source .bash_profile run; echo &#x00022;Start:&#x00022;;date; ${lineitem}; echo &#x00022;End&#x00022;; date" trust="true" output="${installation_logs}/${branch_name}/${package_name}/${host_name}/MD120_f694_f695_install.log" append="true" failonerror="false" verbose="false" timeout="60000"/>
		<echo message=""/>	 		
	</target>	

<!-- End of targets for MD120 xxfe_f694 and xxfe_f695 -->	

<!-- Start of targets for shared pool flush -->

	<target name="shared_pool_flush" depends="clock">
		<echo message="==================================================================================================================================="/>
		<echo message="======================================================= shared_pool_flush ========================================================="/>
		<echo message=""/>			
		<echo message="B_Start: ${clock}"/>	
		<echo message=""/>	

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>

 		<sshexec host="${aphost}" username="${apuser}" password="${appasswd}" command="source .bash_profile run; echo &#x00022;Start:&#x00022;;date; echo &quot;alter system flush shared_pool &#x0003B; &quot; | sqlplus apps/${appspasswd}; echo &#x00022;End&#x00022;; date" output="${installation_logs}/${branch_name}/${package_name}/${host_name}/shared_pool_flush.log" append="false" failonerror="false" verbose="false" timeout="60000" trust="true"/>

		<echo message=""/>	 		
		<echo message="B_End: ${clock}"/>
		<echo message=""/>			
		<echo message="======================================================= shared_pool_flush ========================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>	

<!-- End of targets for shared pool flush -->	

<!-- Start of targets for validating object -->

	<target name="validate_objects" depends="clock">
		<echo message="==================================================================================================================================="/>
		<echo message="========================================================= validate_objects ========================================================"/>
		<echo message=""/>			
		<echo message="B_Start: ${clock}"/>
		<echo message=""/>	

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>
		
		<sshexec host="${dbhost}" username="${dbuser}" password="${dbpasswd}" command="source .bash_profile; echo &#x00022;Start:&#x00022;;date; echo &#x00022;SELECT count &#x00028;&#x0002A;&#x00029; FROM all_objects WHERE status&#x0003D;'INVALID' AND owner&#x0003D;'APPS'&#x0003B;&#x00022; | sqlplus &#x00022;/as sysdba&#x00022;; echo &#x00022; &#x00040;&#x0003F;/rdbms/admin/utlrp.sql &#x0003B; &#x00022; | sqlplus &#x00022;/as sysdba&#x00022;; echo &#x00022;SELECT count &#x00028;&#x0002A;&#x00029; FROM all_objects WHERE status&#x0003D;'INVALID' AND owner&#x0003D;'APPS'&#x0003B;&#x00022; | sqlplus &#x00022;/as sysdba&#x00022;; echo &#x00022;End&#x00022;; date" output="${installation_logs}/${branch_name}/${package_name}/${host_name}/validate_objects.log" append="false" failonerror="false" verbose="false" timeout="12000000" trust="true"/>

		<echo message=""/>			
		<echo message="B_End: ${clock}"/>
		<echo message=""/>			
		<echo message="========================================================= validate_objects ========================================================"/>
		<echo message="==================================================================================================================================="/>
	</target>	

<!-- Start of target for standalone validating object -->

	<target name="validate_objects_standalone" depends="clock">
		<echo message="==================================================================================================================================="/>
		<echo message="==================================================== validate_objects_standalone =================================================="/> 
		<echo message=""/>			
		<echo message="B_Start: ${clock}"/>
		<echo message=""/>	

		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<propertycopy name="host_name" from="hostname"/>

        <tstamp>
        	<format  property="TIMESTAMP"  pattern="yyyy-MM-dd_HH-mm-ss"/>
        </tstamp>

        <mkdir dir="${validate_objects}/${host_name}/${TIMESTAMP}"/>  		
        <mkdir dir="${installation_logs}/validate_objects_standalone/${host_name}/${TIMESTAMP}"/>

		<sshexec host="${dbhost}" username="${dbuser}" password="${dbpasswd}" command="source .bash_profile; echo &#x00022;Start:&#x00022;;date; echo &#x00022;SELECT count &#x00028;&#x0002A;&#x00029; FROM all_objects WHERE status&#x0003D;'INVALID' AND owner&#x0003D;'APPS'&#x0003B;&#x00022; | sqlplus &#x00022;/as sysdba&#x00022;; echo &#x00022; &#x00040;&#x0003F;/rdbms/admin/utlrp.sql &#x0003B; &#x00022; | sqlplus &#x00022;/as sysdba&#x00022;; echo &#x00022;SELECT count &#x00028;&#x0002A;&#x00029; FROM all_objects WHERE status&#x0003D;'INVALID' AND owner&#x0003D;'APPS'&#x0003B;&#x00022; | sqlplus &#x00022;/as sysdba&#x00022;; echo &#x00022;End&#x00022;; date" output="${validate_objects}/${host_name}/${TIMESTAMP}/validate_objects.log" append="false" failonerror="false" verbose="false" timeout="12000000" trust="true"/>

		<!--"${validate_objects}/${host_name}/validate_objects.log"-->

    	<copy includeemptydirs="false" todir="${installation_logs}/validate_objects_standalone/${host_name}/${TIMESTAMP}/">
            <fileset dir="${validate_objects}/${host_name}/${TIMESTAMP}/"/>
    	</copy> 		

		<echo message=""/>			
		<echo message="B_End: ${clock}"/>
		<echo message=""/>			
		<echo message="==================================================== validate_objects_standalone =================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>	

<!-- End of target for standalone validating object -->	

<!-- End of targets for validating object -->	


<!-- Start of targets for JAR signer -->

	<target name="JAR_signer" depends="clock">
		<echo message="==================================================================================================================================="/>
		<echo message="============================================================ JAR_signer ==========================================================="/>
		<echo message=""/>			
		<echo message="B_Start: ${clock}"/>
		<echo message=""/>	

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>

 		<sshexec host="${aphost}" username="${apuser}" password="${appasswd}" command="source .bash_profile run; echo &#x00022;Start:&#x00022;;date; cd $JAVA_TOP; mv customprod.jar customprod-`date -I`.jar; zip -r customprod.zip com org oracle/apps/bne/* oracle/apps/xxfe/* oracle/apps/xxfi/* billing tools oracle/jsp/* oracle/calypso/* oracle/sqlj/*; adjava oracle.apps.ad.jri.adjmx -areas $JAVA_TOP/customprod.zip -outputFile $JAVA_TOP/customprod.jar -jar $CONTEXT_NAME 1 CUST jarsigner -storePass puneet -keyPass myxuan; rm customprod.zip; echo &#x00022;End&#x00022;; date" trust="true" output="${installation_logs}/${branch_name}/${package_name}/${host_name}/JAR_signer.log" append="false" failonerror="false" verbose="false" timeout="60000"/>

		<echo message=""/>	 		
		<echo message="B_End: ${clock}"/> 
		<echo message=""/>			
		<echo message="============================================================ JAR_signer ==========================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>	


<!-- End of targets for JAR signer -->			


<!-- Start of targets for TUKKU mock -->

	<target name="tukku_mock" depends="clock,set_variables">
		<echo message="==================================================================================================================================="/>
		<echo message="============================================================ tukku_mock ==========================================================="/>
		<echo message=""/>			
		<echo message="B_Start: ${clock}"/>
		<echo message=""/>	

		<antcall target="validate_objects_standalone"/>

		<loadproperties srcFile="${active_deploy_env_property_file}"/>

		<!--New code added on 20190201, just to improve folder structure-->
		<propertycopy name="host_name" from="hostname"/>

        <tstamp>
        	<format  property="TIMESTAMP"  pattern="yyyy-MM-dd_HH-mm-ss"/>
        </tstamp>

        <mkdir dir="${tukku}/${host_name}/${TIMESTAMP}"/>
        <mkdir dir="${installation_logs}/tukku_mock/${host_name}/${TIMESTAMP}"/>
		<!--New code added on 20190201-->        		

		<shellscript shell="bash">
			sh "${utility_space}"/tukku_build_checkout.sh ${hostname}
		</shellscript>	

 		<sshexec host="${aphost}" username="${apuser}" password="${appasswd}" command="source .bash_profile run; echo &#x00022;Start:&#x00022;;date; echo &#x00022;&#x0003C;perlpath&#x0003E;&#x00022;; which perl;echo &#x00022;&#x0003C;&#x0002F;perlpath&#x0003E;&#x00022;; echo &#x00022;End&#x00022;; date" trust="true" output="${tukku}/${host_name}/${TIMESTAMP}/tukku_mock.log" append="false" failonerror="false" verbose="false" timeout="60000"/>

 		<!--"${tukku}/${hostname}/tukku.log"-->

 		<loadfile property="file" srcfile="${tukku}/${host_name}/${TIMESTAMP}/tukku_mock.log"/>
 		<propertyregex property="perlpath" input="${file}" regexp="\Wperlpath\W(\s)([^\*]*)\W\/perlpath\W" select="\2" override="true"/>
 		<echo message="${perlpath}"/>

 		<replace file="${tukku}/${host_name}/t04/XXFTESTENV_T04_TUKKU_MOCK.prog" token="/usr/bin/perl" value="${perlpath}"/>

 		<fixcrlf srcdir="${tukku}/${host_name}/t04" includes="*.*" eol="unix"/>

 		<sshexec host="${aphost}" username="${apuser}" password="${appasswd}" command="source .bash_profile run; echo &#x00022;Start:&#x00022;;date; cd $XXFI_TOP/bin; rm -f XXFI_I482_EBS_TO_TUKKU XXFI_I482_EBS_TO_TUKKU.prog XXFTESTENV_T04_TUKKU_MOCK XXFTESTENV_T04_TUKKU_MOCK.prog XXFI_I482_TUKKU_TO_EBS XXFI_I482_TUKKU_TO_EBS.prog tukku_mock_cp.ldt tukku_mock_rg.ldt; echo &#x00022;End&#x00022;; date" trust="true" output="${tukku}/${host_name}/${TIMESTAMP}/tukku_mock.log" append="true" failonerror="false" verbose="false" timeout="60000"/>

		<antcall target="scp_to_remote">
			<param name="sourcedir" value="${tukku}/${hostname}/t04"/>
			<param name="sourcefile" value="*.*"/>
			<param name="hostdir" value="${xxfitop_host_path}/bin"/>
			<param name="rusr" value="${apuser}"/>
			<param name="rpwd" value="${appasswd}"/>
			<param name="rhost" value="${aphost}"/>			
		</antcall>

		<sshexec host="${aphost}" username="${apuser}" password="${appasswd}" command="source .bash_profile run; echo &#x00022;Start:&#x00022;;date; mkdir -p $HOME/../../outdir/xxfi/data/in/xxfi_i482_in/Archive; mkdir -p $HOME/../../outdir/xxfi/data/out/xxfi_i482_out/Archive; mkdir -p $HOME/../../outdir/xxfi/data/archive/xxfi_i482_arch; cd $XXFI_TOP/bin; ln -fs $FND_TOP/$APPLBIN/fndcpesr XXFTESTENV_T04_TUKKU_MOCK; ln -fs $FND_TOP/$APPLBIN/fndcpesr XXFI_I482_EBS_TO_TUKKU; ln -fs $FND_TOP/$APPLBIN/fndcpesr XXFI_I482_TUKKU_TO_EBS; chmod 0777 XXFI_I482_EBS_TO_TUKKU; chmod 0777 XXFI_I482_EBS_TO_TUKKU.prog; chmod 0777 XXFTESTENV_T04_TUKKU_MOCK; chmod 0777 XXFTESTENV_T04_TUKKU_MOCK.prog; chmod 0777 XXFI_I482_TUKKU_TO_EBS.prog; chmod 0777 XXFI_I482_TUKKU_TO_EBS; chmod 0777 tukku_mock_cp.ldt; chmod 0777 tukku_mock_rg.ldt; $FND_TOP/bin/FNDLOAD apps/${appspasswd}@${hostname} 0 Y UPLOAD $FND_TOP/patch/115/import/afcpprog.lct tukku_mock_cp.ldt; $FND_TOP/bin/FNDLOAD apps/${appspasswd}@${hostname} 0 Y UPLOAD $FND_TOP/patch/115/import/afcpreqg.lct tukku_mock_rg.ldt; echo &#x00022;End&#x00022;; date" trust="true" output="${tukku}/${host_name}/${TIMESTAMP}/tukku_mock.log" append="true" failonerror="false" verbose="false" timeout="60000"/>	

		<!--sshexec host="${aphost}" username="${apuser}" password="${appasswd}" command="source .bash_profile run; echo &#x00022;Start:&#x00022;;date; cd $XXFI_TOP/bin; scp $XXFI_TOP/install/i482/bin/XXFI_I482_TUKKU_TO_EBS.prog $XXFI_TOP/bin; ln -fs $FND_TOP/$APPLBIN/fndcpesr XXFTESTENV_T04_TUKKU_MOCK; ln -fs $FND_TOP/$APPLBIN/fndcpesr XXFI_I482_EBS_TO_TUKKU; ln -fs $FND_TOP/$APPLBIN/fndcpesr XXFI_I482_TUKKU_TO_EBS; chmod 0777 XXFI_I482_EBS_TO_TUKKU; chmod 0777 XXFI_I482_EBS_TO_TUKKU.prog; chmod 0777 XXFTESTENV_T04_TUKKU_MOCK; chmod 0777 XXFTESTENV_T04_TUKKU_MOCK.prog; chmod 0777 XXFI_I482_TUKKU_TO_EBS.prog; chmod 0777 XXFI_I482_TUKKU_TO_EBS; chmod 0777 tukku_mock_cp.ldt; chmod 0777 tukku_mock_rg.ldt; $FND_TOP/bin/FNDLOAD apps/${appspasswd}@${hostname} 0 Y UPLOAD $FND_TOP/patch/115/import/afcpprog.lct tukku_mock_cp.ldt; $FND_TOP/bin/FNDLOAD apps/${appspasswd}@${hostname} 0 Y UPLOAD $FND_TOP/patch/115/import/afcpreqg.lct tukku_mock_rg.ldt; echo &#x00022;End&#x00022;; date" trust="true" output="${tukku}/${host_name}/${TIMESTAMP}/tukku.log" append="false" failonerror="false" verbose="false" timeout="60000"/-->

    	<copy includeemptydirs="false" todir="${installation_logs}/tukku_mock/${host_name}/${TIMESTAMP}/t04">
            <fileset dir="${tukku}/${host_name}/${TIMESTAMP}/"/>
            <fileset dir="${tukku}/${host_name}/t04"/>            
    	</copy>	

		<antcall target="validate_objects_standalone"/>		

		<echo message=""/>	
		<echo message="B_End: ${clock}"/> 
		<echo message=""/>			
		<echo message="============================================================ tukku_mock ==========================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>

	<target name="tukku_live" depends="clock,set_variables">
		<echo message="==================================================================================================================================="/>
		<echo message="============================================================ tukku_live ==========================================================="/>
		<echo message=""/>			
		<echo message="B_Start: ${clock}"/>
		<echo message=""/>	

		<antcall target="validate_objects_standalone"/>		

		<loadproperties srcFile="${active_deploy_env_property_file}"/>

		<!--New code added on 20190201, just to improve folder structure-->
		<propertycopy name="host_name" from="hostname"/>

        <tstamp>
        	<format  property="TIMESTAMP"  pattern="yyyy-MM-dd_HH-mm-ss"/>
        </tstamp>

        <mkdir dir="${tukku}/${host_name}/${TIMESTAMP}"/>
        <mkdir dir="${installation_logs}/tukku_live/${host_name}/${TIMESTAMP}"/> 
		<!--New code added on 20190201-->  		

 		<sshexec host="${aphost}" username="${apuser}" password="${appasswd}" command="source .bash_profile run; echo &#x00022;Start:&#x00022;;date; cd $XXFI_TOP/bin; rm -f XXFI_I482_EBS_TO_TUKKU XXFTESTENV_T04_TUKKU_MOCK XXFI_I482_TUKKU_TO_EBS; echo &#x00022;End&#x00022;; date" trust="true" output="${tukku}/${host_name}/${TIMESTAMP}/tukku_live.log" append="false" failonerror="false" verbose="false" timeout="60000"/>

   	    <sshexec host="${aphost}" username="${apuser}" password="${appasswd}" command="source .bash_profile run; echo &#x00022;Start:&#x00022;;date; $XXFI_TOP/install/i482/xxfi_i482_setup_master.sh ${appspasswd} ${schemapasswd} f; $XXFI_TOP/install/i482/xxfi_i482_setup_master2.sh ${appspasswd} ${schemapasswd} f; echo &#x00022;End&#x00022;; date" trust="true" output="${tukku}/${host_name}/${TIMESTAMP}/tukku_live.log" append="true" failonerror="false" verbose="false" timeout="60000"/>

    	<copy includeemptydirs="false" todir="${installation_logs}/tukku_live/${host_name}/${TIMESTAMP}/">
            <fileset dir="${tukku}/${host_name}/${TIMESTAMP}/"/>            
    	</copy>	   	    

		<antcall target="validate_objects_standalone"/>   	    

		<echo message=""/>	
		<echo message="B_End: ${clock}"/> 
		<echo message=""/>			
		<echo message="============================================================ tukku_live ==========================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>		

<!-- End of targets for TUKKU mock -->	

<!-- Start of targets for Middle tier restart -->

	<target name="middle_tier_restart" depends="clock">

		<antcall target="JAR_signer"/>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>			

		<antcall target="check_status">
			<param name="module_to_check_status" value="JAR_signer_status"/>
			<param name="status_to_check" value="End"/>
			<param name="time_frequency_to_check_status" value="30s"/>
			<param name="count_frequency_to_check_status" value="2"/>
			<param name="brch_name" value="${branch_name}"/>							
			<param name="pkg_name" value="${package_name}"/>		
			<param name="hst_name" value="${host_name}"/>							
			<param name="property_to_set" value="JAR_signer"/>
		</antcall>

		<var name="JAR_signer" unset="true"/>
		<loadproperties srcFile="${installation_status_property_file}"/>		
		<propertycopy name="JAR_signer" from="JAR_signer"/>
		<echo message="JAR_signer: ${JAR_signer}"/>

		<if>
			<equals arg1="${JAR_signer}" arg2="0"/>
				<then>
					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="================================= JAR_signer Done ========================================"/>
					<echo message="===================================== ! Info ! ==========================================="/>
				</then>
		<else>
				<echo message="===================================== ! Alert ! =========================================="/>			
				<echo message="================= JAR_signer not done yet. Recommend manual invtervention ================"/>
				<echo message="===================================== ! Alert ! =========================================="/>				
		</else>
		</if>


		<echo message="==================================================================================================================================="/>
		<echo message="====================================================== middle_tier_restart ========================================================"/>
		<echo message=""/>			
		<echo message="B_Start: ${clock}"/>
		<echo message=""/>

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>				

		<antcall target="ebs_web_shutdown"/>
		<!--antcall target="check_ebs_web_status"/-->

		<antcall target="check_status">
			<param name="module_to_check_status" value="ebs_web_status"/>
			<param name="status_to_check" value="Down"/>
			<param name="time_frequency_to_check_status" value="30s"/>
			<param name="count_frequency_to_check_status" value="2"/>
			<param name="brch_name" value="${branch_name}"/>							
			<param name="pkg_name" value="${package_name}"/>		
			<param name="hst_name" value="${host_name}"/>							
			<param name="property_to_set" value="ebs_web_shutdown"/>
		</antcall>

		<var name="ebs_web_shutdown" unset="true"/>
		<loadproperties srcFile="${installation_status_property_file}"/>		
		<propertycopy name="ebs_web_shutdown" from="ebs_web_shutdown"/>
		<echo message="ebs_web_shutdown: ${ebs_web_shutdown}"/>

		<if>
			<equals arg1="${ebs_web_shutdown}" arg2="0"/>
				<then>
					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="=================================== ebs_web down ========================================="/>
					<echo message="===================================== ! Info ! ==========================================="/>
				</then>
		<else>
				<echo message="===================================== ! Alert ! =========================================="/>			
				<echo message="================= ebs_web not down yet. Recommend manual invtervention ==================="/>
				<echo message="===================================== ! Alert ! =========================================="/>				
		</else>
		</if>

		<antcall target="oacore_shutdown"/>
		<!--antcall target="check_oacore_status"/-->

		<antcall target="check_status">
			<param name="module_to_check_status" value="oacore_status"/>
			<param name="status_to_check" value="shutdown"/>
			<param name="time_frequency_to_check_status" value="30s"/>
			<param name="count_frequency_to_check_status" value="2"/>
			<param name="brch_name" value="${branch_name}"/>							
			<param name="pkg_name" value="${package_name}"/>		
			<param name="hst_name" value="${host_name}"/>							
			<param name="property_to_set" value="oacore_shutdown"/>
		</antcall>

		<var name="oacore_shutdown" unset="true"/>
		<loadproperties srcFile="${installation_status_property_file}"/>		
		<propertycopy name="oacore_shutdown" from="oacore_shutdown"/>
		<echo message="oacore_shutdown: ${oacore_shutdown}"/>

		<if>
			<equals arg1="${oacore_shutdown}" arg2="0"/>
				<then>
					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="=================================== oacore down =========================================="/>
					<echo message="===================================== ! Info ! ==========================================="/>
				</then>
		<else>
				<echo message="===================================== ! Alert ! =========================================="/>			
				<echo message="=================== oacore not down yet. Recommend manual invtervention =================="/>
				<echo message="===================================== ! Alert ! =========================================="/>				
		</else>
		</if>

		<antcall target="oacore_start"/>
		<!--antcall target="check_oacore_status"/-->

		<antcall target="check_status">
			<param name="module_to_check_status" value="oacore_status"/>
			<param name="status_to_check" value="running."/>
			<param name="time_frequency_to_check_status" value="30s"/>
			<param name="count_frequency_to_check_status" value="2"/>
			<param name="brch_name" value="${branch_name}"/>							
			<param name="pkg_name" value="${package_name}"/>		
			<param name="hst_name" value="${host_name}"/>							
			<param name="property_to_set" value="oacore_start"/>
		</antcall>

		<var name="oacore_start" unset="true"/>
		<loadproperties srcFile="${installation_status_property_file}"/>		
		<propertycopy name="oacore_start" from="oacore_start"/>
		<echo message="oacore_start: ${oacore_start}"/>

		<if>
			<equals arg1="${oacore_start}" arg2="0"/>
				<then>
					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="===================================== oacore up =========================================="/>
					<echo message="===================================== ! Info ! ==========================================="/>
				</then>
		<else>
				<echo message="===================================== ! Alert ! =========================================="/>			
				<echo message="================== oacore not up yet. Recommend manual invtervention ====================="/>
				<echo message="===================================== ! Alert ! =========================================="/>				
		</else>
		</if>

		<antcall target="ebs_web_start"/>
		<!--antcall target="check_ebs_web_status"/-->

		<antcall target="check_status">
			<param name="module_to_check_status" value="ebs_web_status"/>
			<param name="status_to_check" value="Alive"/>
			<param name="time_frequency_to_check_status" value="30s"/>
			<param name="count_frequency_to_check_status" value="2"/>
			<param name="brch_name" value="${branch_name}"/>							
			<param name="pkg_name" value="${package_name}"/>		
			<param name="hst_name" value="${host_name}"/>							
			<param name="property_to_set" value="ebs_web_start"/>
		</antcall>

		<var name="ebs_web_start" unset="true"/>
		<loadproperties srcFile="${installation_status_property_file}"/>		
		<propertycopy name="ebs_web_start" from="ebs_web_start"/>
		<echo message="ebs_web_start: ${ebs_web_start}"/>

		<if>
			<equals arg1="${ebs_web_start}" arg2="0"/>
				<then>
					<echo message="===================================== ! Info ! ==========================================="/>
					<echo message="==================================== ebs_web up =========================================="/>
					<echo message="===================================== ! Info ! ==========================================="/>
				</then>
		<else>
				<echo message="===================================== ! Alert ! =========================================="/>			
				<echo message="================= ebs_web not up yet. Recommend manual invtervention ====================="/>
				<echo message="===================================== ! Alert ! =========================================="/>				
		</else>
		</if>		

		<echo message=""/>			
		<echo message="B_End: ${clock}"/> 
		<echo message=""/>			
		<echo message="====================================================== middle_tier_restart ========================================================"/>
		<echo message="==================================================================================================================================="/>
	</target>	


<!-- End of targets for Middle tier restart -->

<!-- Start of targets for CM restart -->

	<target name="cm_restart" depends="clock">
		<echo message="==================================================================================================================================="/>
		<echo message="============================================== cm_restart and shared_pool_flush ==================================================="/>	
		<echo message=""/>				
		<echo message="B_Start: ${clock}"/>
		<echo message=""/>	

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${app_build_property_file}"/>		
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="host_name" from="hostname"/>					

		<antcall target="concurrent_manager_shutdown"/>	

		<antcall target="check_status">
			<param name="module_to_check_status" value="concurrent_manager_status"/>
			<param name="status_to_check" value="Not Active at this time"/>
			<param name="time_frequency_to_check_status" value="3m"/>
			<param name="count_frequency_to_check_status" value="5"/>
			<param name="brch_name" value="${branch_name}"/>							
			<param name="pkg_name" value="${package_name}"/>		
			<param name="hst_name" value="${host_name}"/>							
			<param name="property_to_set" value="concurrent_manager_shutdown"/>
		</antcall>

		<var name="concurrent_manager_shutdown" unset="true"/>
		<loadproperties srcFile="${installation_status_property_file}"/>		
		<propertycopy name="concurrent_manager_shutdown" from="concurrent_manager_shutdown"/>
		<echo message="concurrent_manager_shutdown: ${concurrent_manager_shutdown}"/>

		<if>
			<equals arg1="${concurrent_manager_shutdown}" arg2="0"/>
				<then>
					<echo message="===================================== ! Info ! ==========================================="/>							
					<echo message="=============================== concurrent manager down =================================="/>
					<echo message="===================================== ! Info ! ==========================================="/>

					<antcall target="shared_pool_flush"/>

					<antcall target="check_status">
						<param name="module_to_check_status" value="shared_pool_flush_status"/>
						<param name="status_to_check" value="System altered"/>
						<param name="time_frequency_to_check_status" value="30s"/>
						<param name="count_frequency_to_check_status" value="2"/>
						<param name="brch_name" value="${branch_name}"/>							
						<param name="pkg_name" value="${package_name}"/>		
						<param name="hst_name" value="${host_name}"/>							
						<param name="property_to_set" value="shared_pool_flush"/>
					</antcall>

					<var name="shared_pool_flush" unset="true"/>
					<loadproperties srcFile="${installation_status_property_file}"/>		
					<propertycopy name="shared_pool_flush" from="shared_pool_flush"/>
					<echo message="shared_pool_flush: ${shared_pool_flush}"/>

					<if>
						<equals arg1="${shared_pool_flush}" arg2="0"/>
							<then>
								<echo message="===================================== ! Info ! ==========================================="/>
								<echo message="============================= Shared pool flush Done ====================================="/>
								<echo message="===================================== ! Info ! ==========================================="/>
							</then>
							<else>
								<echo message="===================================== ! Alert ! =========================================="/>			
								<echo message="============= Shared pool flush not done yet. Recommend manual invtervention ============="/>
								<echo message="===================================== ! Alert ! =========================================="/>				
							</else>
					</if>					
					
					<antcall target="concurrent_manager_start"/>
					
					<antcall target="check_status">
						<param name="module_to_check_status" value="concurrent_manager_status"/>
						<param name="status_to_check" value="Active"/>
						<param name="time_frequency_to_check_status" value="3m"/>
						<param name="count_frequency_to_check_status" value="5"/>
						<param name="brch_name" value="${branch_name}"/>							
						<param name="pkg_name" value="${package_name}"/>		
						<param name="hst_name" value="${host_name}"/>										
						<param name="property_to_set" value="concurrent_manager_start"/>
					</antcall>

					<var name="concurrent_manager_start" unset="true"/>
					<loadproperties srcFile="${installation_status_property_file}"/>		
					<propertycopy name="concurrent_manager_start" from="concurrent_manager_start"/>
					<echo message="concurrent_manager_start: ${concurrent_manager_start}"/>

					<if>
						<equals arg1="${concurrent_manager_start}" arg2="0"/>
							<then>
								<echo message="===================================== ! Info ! ==========================================="/>
								<echo message="================================ concurrent manager up ==================================="/>
								<echo message="===================================== ! Info ! ==========================================="/>								
							</then>
					<else>
							<echo message="===================================== ! Alert ! =========================================="/>
							<echo message="========= Unable to bring concurrent manager up. Recommend manual invtervention =========="/>
							<echo message="===================================== ! Alert ! =========================================="/>							
					</else>
					</if>

				</then>
		<else>
				<echo message="============================ concurrent_manager_abort =============================="/>				
				
				<antcall target="concurrent_manager_abort"/>
				
				<antcall target="check_status">
					<param name="module_to_check_status" value="concurrent_manager_status"/>
					<param name="status_to_check" value="Not Active at this time"/>
					<param name="time_frequency_to_check_status" value="3m"/>
					<param name="count_frequency_to_check_status" value="5"/>
					<param name="brch_name" value="${branch_name}"/>							
					<param name="pkg_name" value="${package_name}"/>		
					<param name="hst_name" value="${host_name}"/>									
					<param name="property_to_set" value="concurrent_manager_shutdown"/>
				</antcall>

				<var name="concurrent_manager_shutdown" unset="true"/>
				<loadproperties srcFile="${installation_status_property_file}"/>		
				<propertycopy name="concurrent_manager_shutdown" from="concurrent_manager_shutdown"/>
				<echo message="concurrent_manager_shutdown: ${concurrent_manager_shutdown}"/>

				<if>
					<equals arg1="${concurrent_manager_shutdown}" arg2="0"/>
						<then>
								<echo message="===================================== ! Info ! ==========================================="/>							
								<echo message="=============================== concurrent manager down =================================="/>
								<echo message="===================================== ! Info ! ==========================================="/>							

								<antcall target="shared_pool_flush"/>

								<antcall target="check_status">
									<param name="module_to_check_status" value="shared_pool_flush_status"/>
									<param name="status_to_check" value="System altered"/>
									<param name="time_frequency_to_check_status" value="30s"/>
									<param name="count_frequency_to_check_status" value="2"/>
									<param name="brch_name" value="${branch_name}"/>							
									<param name="pkg_name" value="${package_name}"/>		
									<param name="hst_name" value="${host_name}"/>							
									<param name="property_to_set" value="shared_pool_flush"/>
								</antcall>

								<var name="shared_pool_flush" unset="true"/>
								<loadproperties srcFile="${installation_status_property_file}"/>		
								<propertycopy name="shared_pool_flush" from="shared_pool_flush"/>
								<echo message="shared_pool_flush: ${shared_pool_flush}"/>

								<if>
									<equals arg1="${shared_pool_flush}" arg2="0"/>
										<then>
											<echo message="===================================== ! Info ! ==========================================="/>
											<echo message="============================= Shared pool flush Done ====================================="/>
											<echo message="===================================== ! Info ! ==========================================="/>
										</then>
									<else>
										<echo message="===================================== ! Alert ! =========================================="/>			
										<echo message="============= Shared pool flush not done yet. Recommend manual invtervention ============="/>
										<echo message="===================================== ! Alert ! =========================================="/>				
									</else>
								</if>

								<antcall target="concurrent_manager_start"/>

								<antcall target="check_status">
									<param name="module_to_check_status" value="concurrent_manager_status"/>
									<param name="status_to_check" value="Active"/>
									<param name="time_frequency_to_check_status" value="3m"/>
									<param name="count_frequency_to_check_status" value="5"/>
									<param name="brch_name" value="${branch_name}"/>							
									<param name="pkg_name" value="${package_name}"/>		
									<param name="hst_name" value="${host_name}"/>			
									<param name="property_to_set" value="concurrent_manager_start"/>
								</antcall>

								<var name="concurrent_manager_start" unset="true"/>
								<loadproperties srcFile="${installation_status_property_file}"/>		
								<propertycopy name="concurrent_manager_start" from="concurrent_manager_start"/>
								<echo message="concurrent_manager_start: ${concurrent_manager_start}"/>

								<if>
									<equals arg1="${concurrent_manager_start}" arg2="0"/>
										<then>
											<echo message="===================================== ! Info ! ==========================================="/>
											<echo message="================================ concurrent manager up ==================================="/>
											<echo message="===================================== ! Info ! ==========================================="/>
										</then>
								<else>
										<echo message="===================================== ! Alert ! =========================================="/>
										<echo message="========= Unable to bring concurrent manager up. Recommend manual invtervention =========="/>
										<echo message="===================================== ! Alert ! =========================================="/>
								</else>
								</if>

						</then>
				<else>
						<echo message="===================================== ! Alert ! =========================================="/>					
						<echo message="========= Unable to take concurrent manager down. Recommend manual invtervention ========="/>
						<echo message="===================================== ! Alert ! =========================================="/>				
				</else>
				</if>
		</else>
		</if>		
		<echo message=""/>	
		<echo message="B_End: ${clock}"/>
		<echo message=""/>			
		<echo message="============================================== cm_restart and shared_pool_flush ==================================================="/>
		<echo message="==================================================================================================================================="/>
		
		<antcall target="validate_objects"/>

		<antcall target="check_invalid_objects">
			<param name="installation_state" value="AFTER_ENV_RESTARTS"/>
		</antcall>

	</target>

<!-- End of targets for CM restart -->

<!--**************************************** END OF BASELINED CORE TARGETS ****************************************************-->

<!--**************************************** START OF TARGETS UNDER STUDY ****************************************************-->

<!-- Start of target to check port status -->

	<target name="a" depends="clock">
		<echo message="B_Start: ${clock}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<propertycopy name="package_name" from="package.name"/>

 		<condition property="WLS_Admin_Server" value="UP" else="DOWN">
 			<socket server="sehan9507ca.han.telia.se" port="7063"/>
 		</condition>

 		<echo message="WLS_Admin_Server : ${WLS_Admin_Server}"/>

 		<condition property="WLS_OACORE_Application" value="UP" else="DOWN">
 			<socket server="sehan9507ca.han.telia.se" port="7263"/>
 		</condition>

 		<echo message="WLS_OACORE_Application : ${WLS_OACORE_Application}"/>

 		<condition property="WLS_FORMS_Application" value="UP" else="DOWN">
 			<socket server="sehan9507ca.han.telia.se" port="7463"/>
 		</condition>

 		<echo message="WLS_FORMS_Application : ${WLS_FORMS_Application}"/>

 		<condition property="WLS_OAFM_Application" value="UP" else="DOWN">
 			<socket server="sehan9507ca.han.telia.se" port="7663"/>
 		</condition>

 		<echo message="WLS_OAFM_Application : ${WLS_OAFM_Application}"/>

 		<condition property="WLS_FORMS-C4WS_Application" value="UP" else="DOWN">
 			<socket server="sehan9507ca.han.telia.se" port="7863"/>
 		</condition>

 		<echo message="WLS_FORMS-C4WS_Application : ${WLS_FORMS-C4WS_Application}"/>

 		<condition property="OHS_Administration_Proxy" value="UP" else="DOWN">
 			<socket server="sehan9507ca.han.telia.se" port="10061"/>
 		</condition>

 		<echo message="OHS_Administration_Proxy : ${OHS_Administration_Proxy}"/>

 		<condition property="Node_Manager_Port" value="UP" else="DOWN">
 			<socket server="sehan9507ca.han.telia.se" port="7063"/>
 		</condition>

 		<echo message="Node_Manager_Port : ${Node_Manager_Port}"/>

 		<condition property="Database_Port" value="UP" else="DOWN">
 			<socket server="sehan9507ca.han.telia.se" port="7263"/>
 		</condition>

 		<echo message="Database_Port : ${Database_Port}"/>

 		<condition property="RPC_Port" value="UP" else="DOWN">
 			<socket server="sehan9507ca.han.telia.se" port="7463"/>
 		</condition>

 		<echo message="RPC_Port : ${RPC_Port}"/>

 		<condition property="WEB_SSL_port" value="UP" else="DOWN">
 			<socket server="sehan9507ca.han.telia.se" port="7663"/>
 		</condition>

 		<condition property="ONS_Local_port" value="UP" else="DOWN">
 			<socket server="sehan9507ca.han.telia.se" port="7863"/>
 		</condition>

 		<echo message="ONS_Local_port : ${ONS_Local_port}"/> 		

 		<condition property="ONS_Remote_port" value="UP" else="DOWN">
 			<socket server="sehan9507ca.han.telia.se" port="10061"/>
 		</condition>

 		<echo message="ONS_Remote_port : ${ONS_Remote_port}"/> 	 		

 		<condition property="Active_web_port" value="UP" else="DOWN">
 			<socket server="sehan9507ca.han.telia.se" port="7863"/>
 		</condition>

 		<echo message="Active_web_port : ${Active_web_port}"/>  		

 		<condition property="Forms_port" value="UP" else="DOWN">
 			<socket server="sehan9507ca.han.telia.se" port="10061"/>
 		</condition>  

 		<echo message="Forms_port : ${Forms_port}"/>  		

 		<condition property="Metrics_Server_Data_Port" value="UP" else="DOWN">
 			<socket server="sehan9507ca.han.telia.se" port="7863"/>
 		</condition>

 		<echo message="Metrics_Server_Data_Port : ${Metrics_Server_Data_Port}"/>  	 		

 		<condition property="Metrics_Server_Request_Port" value="UP" else="DOWN">
 			<socket server="sehan9507ca.han.telia.se" port="10061"/>
 		</condition>

 		<echo message="Metrics_Server_Request_Port : ${Metrics_Server_Request_Port}"/>  	

 		<condition property="JTF_Fulfillment_Server_Port" value="UP" else="DOWN">
 			<socket server="sehan9507ca.han.telia.se" port="7863"/>
 		</condition>

 		<echo message="JTF_Fulfillment_Server_Port : ${JTF_Fulfillment_Server_Port}"/>

 		<condition property="MSCA_Dispatcher_Port" value="UP" else="DOWN">
 			<socket server="sehan9507ca.han.telia.se" port="10061"/>
 		</condition>

 		<echo message="MSCA_Dispatcher_Port : ${MSCA_Dispatcher_Port}"/>    		

 		<condition property="MSCA_Server_Port" value="UP" else="DOWN">
 			<socket server="sehan9507ca.han.telia.se" port="7863"/>
 		</condition>

 		<echo message="MSCA_Server_Port : ${MSCA_Server_Port}"/>    		

 		<condition property="Java_Object_Cache_Port" value="UP" else="DOWN">
 			<socket server="sehan9507ca.han.telia.se" port="10061"/>
 		</condition>

 		<echo message="Java_Object_Cache_Port : ${Java_Object_Cache_Port}"/>   		

 		<condition property="Oracle_Connection_Manager_Port" value="UP" else="DOWN">
 			<socket server="sehan9507ca.han.telia.se" port="10061"/>
 		</condition> 	

 		<echo message="Oracle_Connection_Manager_Port : ${Oracle_Connection_Manager_Port}"/>  
		<echo message="B_End: ${clock}"/>		
	</target>		

<!-- End of target to check port status -->

	<property name="robot_test" location="../../../../Robot/Project_2/"/>

	<target name="schedule_concurrent_coreprocess" depends="clock">
		<echo message="==================================================================================================================================="/>
		<echo message="======================================================= schedule_concurrent_coreprocess ======================================================"/>
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>			
		<echo message=""/>

		<sleep seconds="10"/>
		<echo message="${lineitem}"/>
		<propertyregex property="concurrent_name" input="${lineitem}" regexp="([A-Za-z0-9_-]+)\s([A-Za-z0-9_-]+)" select="\1" override="true"/>
		<propertyregex property="schedule_concurrent_test_name" input="${lineitem}" regexp="([A-Za-z0-9_-]+)\s([A-Za-z0-9_-]+)" select="\2" override="true"/>
		<echo message="${concurrent_name}"/>
		<echo message="${schedule_concurrent_test_name}"/>		

		<shellscript shell="bash">

			applicationurl=`grep applicationurl "${active_deploy_env_property_file}" | cut -d '=' -f 2`;
			echo "$applicationurl";
			applicationurl=`echo "$applicationurl" | tr -d '\\'`;
			echo "$applicationurl";

			applicationuser=`grep applicationuser "${active_deploy_env_property_file}" | cut -d '=' -f 2`;
			echo "$applicationuser";

			applicationpasswd=`grep applicationpasswd "${active_deploy_env_property_file}" | cut -d '=' -f 2`;
			echo "$applicationpasswd";

			cd "${robot_test}";

			rm -r reports;

			robot -d reports -v url:"${applicationurl}" -v user:"${schedule_concurrent_test_name}" -v password:"${schedule_concurrent_test_name}" -t temp1 Test_2.robot; 

		</shellscript>	

		<!--loadproperties srcFile="${active_deploy_env_property_file}"/

			robot -d reports -v url:"${applicationurl}" -v user:"${applicationuser}" -v password:"${applicationpasswd}" -v concurrent_name:"${XXFI_I482_CREATE_TUKKU_FILE}" -t CHK_CC_EXIST_FOR_CANCELLATION Test_2.robot; 

			robot -d reports -v url:"${applicationurl}" -v user:"${applicationuser}" -v password:"${applicationpasswd}" -t temp1 Test_2.robot; 
			

	-->

 		<loadfile property="file" srcfile="${robot_test}/reports/output.xml"/>

 		<propertyregex property="test_status" input="${file}" regexp="(&lt;status\sstatus=\&quot;)(PASS|FAIL)([\&quot;\sa-zA-Z0-9=\&quot;:.!]+\&gt;)([\sa-zA-Z0-9=\&quot;:.!]+)(&lt;\/status&gt;)(\r\n&lt;\/test&gt;)" select="\4" override="true"/>

 		<propertyfile file="Flags/Flags.properties">
 			<entry key="test_status" value="${test_status}"/>
 		</propertyfile>

 		<echo message="test_status is ${test_status}"/>

 		<sleep seconds="10"/>
 		<var name="file" unset="true"/>

 		<if>
			<equals arg1="${test_status}" arg2="Good ! New CC can be created"/>
				<then>
						<shellscript shell="bash">

							applicationurl=`grep applicationurl "${active_deploy_env_property_file}" | cut -d '=' -f 2`;
							echo "$applicationurl";
							applicationurl=`echo "$applicationurl" | tr -d '\\'`;
							echo "$applicationurl";

							applicationuser=`grep applicationuser "${active_deploy_env_property_file}" | cut -d '=' -f 2`;
							echo "$applicationuser";

							applicationpasswd=`grep applicationpasswd "${active_deploy_env_property_file}" | cut -d '=' -f 2`;
							echo "$applicationpasswd";

							cd "${robot_test}";

							rm -r reports;

							robot -d reports -v url:"${applicationurl}" -v user:"${schedule_concurrent_test_name}" -v password:"${schedule_concurrent_test_name}" -t temp2 Test_2.robot; 
			
						</shellscript>

						<!-- 							robot -d reports -v url:"${applicationurl}" -v user:"${applicationuser}" -v password:"${applicationpasswd}" -t temp2 Test_2.robot; -->

				 		<loadfile property="file" srcfile="${robot_test}/reports/output.xml"/>

 						<propertyregex property="test_status" input="${file}" regexp="(&lt;status\sstatus=\&quot;)(PASS|FAIL)([\&quot;\sa-zA-Z0-9=\&quot;:.!]+\&gt;)([\sa-zA-Z0-9=\&quot;:.!]+)(&lt;\/status&gt;)(\r\n&lt;\/test&gt;)" select="\4" override="true"/>

 						<propertyfile file="Flags/Flags.properties">
 							<entry key="test_status" value="${test_status}"/>
 						</propertyfile>

 						<echo message="test_status is ${test_status}"/>
				</then>
			<elseif>
				<equals arg1="${test_status}" arg2="Oops ! Looks there are open CC"/>
					<then>
						<echo message="Inside elseif ${test_status}"/>
					</then>
			</elseif>
		</if>

		<echo message=""/> 		
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="======================================================= schedule_concurrent_coreprocess ======================================================"/>
		<echo message="==================================================================================================================================="/>
	</target>		

<!-- End of targets to manage ebs_web -->

	<target name="schedule_concurrent">
		<echo message="==================================================================================================================================="/>
		<echo message="===================================================== schedule_concurrent ==================================================="/>
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>		
		<echo message=""/>		
		<!--echo message="ceb_pack_${package_name}.log"/-->
		<loadfile property="resource" srcfile="${utility_space}/schedule_concurrent_Copy.properties"/>
		<foreach list="${resource}" target="schedule_concurrent_coreprocess" param="lineitem" delimiter="${line.separator}" inheritall="true"/>
		<echo message=""/>			
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="===================================================== schedule_concurrent ==================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>	

	<target name="cancel_concurrent_coreprocess" depends="clock">
		<echo message="==================================================================================================================================="/>
		<echo message="======================================================= cancel_concurrent_coreprocess ======================================================"/>
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>			
		<echo message=""/>

		<sleep seconds="10"/>
		<echo message="${lineitem}"/>
		<propertyregex property="concurrent_name" input="${lineitem}" regexp="([A-Za-z0-9_-]+)\s([A-Za-z0-9_-]+)" select="\1" override="true"/>
		<propertyregex property="cancel_concurrent_test_name" input="${lineitem}" regexp="([A-Za-z0-9_-]+)\s([A-Za-z0-9_-]+)" select="\2" override="true"/>
		<echo message="${concurrent_name}"/>
		<echo message="${cancel_concurrent_test_name}"/>		

		<shellscript shell="bash">

			applicationurl=`grep applicationurl "${active_deploy_env_property_file}" | cut -d '=' -f 2`;
			echo "$applicationurl";
			applicationurl=`echo "$applicationurl" | tr -d '\\'`;
			echo "$applicationurl";

			applicationuser=`grep applicationuser "${active_deploy_env_property_file}" | cut -d '=' -f 2`;
			echo "$applicationuser";

			applicationpasswd=`grep applicationpasswd "${active_deploy_env_property_file}" | cut -d '=' -f 2`;
			echo "$applicationpasswd";

			cd "${robot_test}";

			rm -r reports;

			robot -d reports -v url:"${applicationurl}" -v user:"${schedule_concurrent_test_name}" -v password:"${schedule_concurrent_test_name}" -t temp1 Test_2.robot; 

		</shellscript>	

		<!--loadproperties srcFile="${active_deploy_env_property_file}"/

			robot -d reports -v url:"${applicationurl}" -v user:"${applicationuser}" -v password:"${applicationpasswd}" -v concurrent_name:"${XXFI_I482_CREATE_TUKKU_FILE}" -t CHK_CC_EXIST_FOR_CANCELLATION Test_2.robot; 

			robot -d reports -v url:"${applicationurl}" -v user:"${applicationuser}" -v password:"${applicationpasswd}" -t temp1 Test_2.robot; 
			

	-->

 		<loadfile property="file" srcfile="${robot_test}/reports/output.xml"/>

 		<propertyregex property="test_status" input="${file}" regexp="(&lt;status\sstatus=\&quot;)(PASS|FAIL)([\&quot;\sa-zA-Z0-9=\&quot;:.!]+\&gt;)([\sa-zA-Z0-9=\&quot;:.!]+)(&lt;\/status&gt;)(\r\n&lt;\/test&gt;)" select="\4" override="true"/>

 		<propertyfile file="Flags/Flags.properties">
 			<entry key="test_status" value="${test_status}"/>
 		</propertyfile>

 		<echo message="test_status is ${test_status}"/>

 		<sleep seconds="10"/>
 		<var name="file" unset="true"/>

 		<if>
			<equals arg1="${test_status}" arg2="Good ! New CC can be created"/>
				<then>
						<shellscript shell="bash">

							applicationurl=`grep applicationurl "${active_deploy_env_property_file}" | cut -d '=' -f 2`;
							echo "$applicationurl";
							applicationurl=`echo "$applicationurl" | tr -d '\\'`;
							echo "$applicationurl";

							applicationuser=`grep applicationuser "${active_deploy_env_property_file}" | cut -d '=' -f 2`;
							echo "$applicationuser";

							applicationpasswd=`grep applicationpasswd "${active_deploy_env_property_file}" | cut -d '=' -f 2`;
							echo "$applicationpasswd";

							cd "${robot_test}";

							rm -r reports;

							robot -d reports -v url:"${applicationurl}" -v user:"${schedule_concurrent_test_name}" -v password:"${schedule_concurrent_test_name}" -t temp2 Test_2.robot; 
			
						</shellscript>

						<!-- 							robot -d reports -v url:"${applicationurl}" -v user:"${applicationuser}" -v password:"${applicationpasswd}" -t temp2 Test_2.robot; -->

				 		<loadfile property="file" srcfile="${robot_test}/reports/output.xml"/>

 						<propertyregex property="test_status" input="${file}" regexp="(&lt;status\sstatus=\&quot;)(PASS|FAIL)([\&quot;\sa-zA-Z0-9=\&quot;:.!]+\&gt;)([\sa-zA-Z0-9=\&quot;:.!]+)(&lt;\/status&gt;)(\r\n&lt;\/test&gt;)" select="\4" override="true"/>

 						<propertyfile file="Flags/Flags.properties">
 							<entry key="test_status" value="${test_status}"/>
 						</propertyfile>

 						<echo message="test_status is ${test_status}"/>
				</then>
			<elseif>
				<equals arg1="${test_status}" arg2="Oops ! Looks there are open CC"/>
					<then>
						<echo message="Inside elseif ${test_status}"/>
					</then>
			</elseif>
		</if>

		<echo message=""/> 		
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="======================================================= cancel_concurrent_coreprocess ======================================================"/>
		<echo message="==================================================================================================================================="/>
	</target>		

<!-- End of targets to manage ebs_web -->

	<target name="cancel_concurrent">
		<echo message="==================================================================================================================================="/>
		<echo message="===================================================== cancel_concurrent ==================================================="/>
		<echo message=""/>		
		<echo message="B_Start: ${clock}"/>		
		<echo message=""/>		
		<!--echo message="ceb_pack_${package_name}.log"/-->
		<loadfile property="resource" srcfile="${utility_space}/cancel_concurrent_Copy.properties"/>
		<foreach list="${resource}" target="cancel_concurrent_coreprocess" param="lineitem" delimiter="${line.separator}" inheritall="true"/>
		<echo message=""/>			
		<echo message="B_End: ${clock}"/>
		<echo message=""/>		
		<echo message="===================================================== cancel_concurrent ==================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>		

	<target name="trial">
		<shellscript shell="bash">

			    cd "${git_alpha2_checkout_root}"

				git config --global user.name "Alex T"

				git config --global user.email "alex.z.pandian@teliacompany.com"

				git config --global merge.tool vimdiff

				git config --global diff.tool vimdiff     

		</shellscript>
		<!-- git config system core.longpaths true -->
	</target>

	<target name="aaa">

		<!--property file="${branch_to_build_property_file}"/>
		<property file="${active_deploy_env_property_file}"/>
		<property file="${db_build_property_file}"/>	
		<property file="${app_build_property_file}"/-->		

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${db_build_property_file}"/>	
		<loadproperties srcFile="${app_build_property_file}"/>	

		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="db_package_name" from="db.package.name"/>		

		<echo message="branch_name: ${branch_name}"/>
		<echo message="package_name: ${package_name}"/>
		<echo message="db_package_name: ${db_package_name}"/>		

		<shellscript shell="bash">
              
			package=`cat ./tmp/temp.properties | grep package.name | cut -d '=' -f 2`
			dbpackage=`cat ./tmp/temp_db.properties | grep db.package.name | cut -d '=' -f 2`
			branch=`cat ./tmp/branch.properties | grep git_branch | cut -d '=' -f 2` 

			./utility/setprop.sh "package.name" "${package}-RB" "${app_build_property_file}"
			./utility/setprop.sh "db.package.name" "${dbpackage}-RB" "${db_build_property_file}"   					

        </shellscript>		

		<var name="branch_name" unset="true"/>
		<var name="package_name" unset="true"/>
		<var name="db_package_name" unset="true"/>	       

		<unset file="${branch_to_build_property_file}"/>
		<unset file="${active_deploy_env_property_file}"/>
		<unset file="${db_build_property_file}"/>	
		<unset file="${app_build_property_file}"/>	

		<loadproperties srcFile="${branch_to_build_property_file}"/>
		<loadproperties srcFile="${active_deploy_env_property_file}"/>
		<loadproperties srcFile="${db_build_property_file}"/>	
		<loadproperties srcFile="${app_build_property_file}"/>	

		<propertycopy name="branch_name" from="git_branch"/>
		<propertycopy name="package_name" from="package.name"/>
		<propertycopy name="db_package_name" from="db.package.name"/>	

		<echo message="branch_name: ${branch_name}"/>
		<echo message="package_name: ${package_name}"/>
		<echo message="db_package_name: ${db_package_name}"/>			

	</target>
  
	<target name="create_release_branch"> <!-- New -->
		<echo message="==================================================================================================================================="/>
		<echo message="======================================================== create_release_branch ===================================================="/>

		<property name="rc_type_temp_var" value="not_set_from_command_line"/>

		<if>
			<equals arg1="${rc_type_temp_var}" arg2="not_set_from_command_line" casesensitive="No"/>
				<then>
					<var name="rc_type_temp_var" unset="true"/>
					<input message="Please enter if build should be done for the Base(b) or Major(m) or Patch(p) ?" addProperty="rc_type_temp_var"/>
				</then>		
		</if>


		<if>
			<or>
				<equals arg1="${rc_type_temp_var}" arg2="b" casesensitive="No"/>
				<equals arg1="${rc_type_temp_var}" arg2="m" casesensitive="No"/>
				<equals arg1="${rc_type_temp_var}" arg2="p" casesensitive="No"/>
			</or>
				<then>
					<shellscript shell="bash">

							./utility/setprop.sh "release_type" "${rc_type_temp_var}" "${tmp_rc_branch_property_file}";
						    echo "";
			    			echo "============================== branch included for build ===========================";
			    			echo "";
			    			cat "${tmp_rc_branch_property_file}";
			    			echo "";
			    			echo "============================== branch included for build ===========================";
			    			echo "";
					</shellscript>					
				</then>
			<else>
				<echo message=""/>				
				<echo message="===================================== ! Alert ! =========================================="/>
				<echo message="Supplied value for release_type: ${rc_type_temp_var}"/>				
				<echo message="Expected Base(b) or Major(m) or Patch(p)"/>
				<echo message="===================================== ! Alert ! =========================================="/>
				<echo message=""/>							
			</else>
		</if>


		<antcall target="download_release-version_from_artifactory"/>


		<loadproperties  srcFile="${tmp}/rc.properties"/>
		<loadproperties  srcFile="${properties}/rc-version.properties"/>
		<property file="${release_version_property_file}"/>
		<property file="${tmp_rc_branch_property_file}"/>

		<if>
			<equals arg1="${release_type}" arg2="b" casesensitive="No"/>
				<then>
					<shellscript shell="bash">

						recent_release_version=`grep 'recent_rc_branch' "${release_version_property_file}" | cut -d '=' -f 2`;
						echo "Recent release version: "$recent_release_version;

						regex='DELTA2\-([^\.]*)\.([^\.]*)\.([^\.]*)'

						[[ $recent_release_version =~ $regex ]]

						recent_base_version=${BASH_REMATCH[1]};
						recent_major_version=${BASH_REMATCH[2]};
						recent_patch_version=${BASH_REMATCH[3]};

						echo "Recent base version: "$recent_base_version;
						echo "Recent major version: "$recent_major_version;
						echo "Recent patch version: "$recent_patch_version;

						new_base_version=`expr $recent_base_version + 1`;
						echo "New base version: "$new_base_version;
						new_release_version="DELTA2-"$new_base_version"."$recent_major_version"."$recent_patch_version;
						echo "New release version: "$new_release_version;

						./utility/setprop.sh "recent_rc_branch" "${new_release_version}" "${release_version_property_file}"

					</shellscript>					
				</then>
			</if>

<if>
			<equals arg1="${release_type}" arg2="m" casesensitive="No"/>
				<then>
					<shellscript shell="bash">

						recent_release_version=`grep 'recent_rc_branch' "${release_version_property_file}" | cut -d '=' -f 2`;
						echo "Recent release version: "$recent_release_version;

						regex='DELTA2\-([^\.]*)\.([^\.]*)\.([^\.]*)'

						[[ $recent_release_version =~ $regex ]]

						recent_base_version=${BASH_REMATCH[1]};
						recent_major_version=${BASH_REMATCH[2]};
						recent_patch_version=${BASH_REMATCH[3]};

						echo "Recent base version: "$recent_base_version;
						echo "Recent major version: "$recent_major_version;
						echo "Recent patch version: "$recent_patch_version;

						new_major_version=`expr $recent_major_version + 1`;
						echo "New major version: "$new_major_version;
						new_release_version="DELTA2-"$recent_base_version"."$new_major_version"."$recent_patch_version;
						echo "New release version: "$new_release_version;

						./utility/setprop.sh "recent_rc_branch" "${new_release_version}" "${release_version_property_file}"

					</shellscript>					
				</then>
			</if>
<if>
			<equals arg1="${release_type}" arg2="p" casesensitive="No"/>
				<then>
					<shellscript shell="bash">

						recent_release_version=`grep 'recent_rc_branch' "${release_version_property_file}" | cut -d '=' -f 2`;
						echo "Recent release version: "$recent_release_version;

						regex='DELTA2\-([^\.]*)\.([^\.]*)\.([^\.]*)'

						[[ $recent_release_version =~ $regex ]]

						recent_base_version=${BASH_REMATCH[1]};
						recent_major_version=${BASH_REMATCH[2]};
						recent_patch_version=${BASH_REMATCH[3]};

						echo "Recent base version: "$recent_base_version;
						echo "Recent major version: "$recent_major_version;
						echo "Recent patch version: "$recent_patch_version;

						new_patch_version=`expr $recent_patch_version + 1`;
						echo "New patch version: "$new_patch_version;
						new_release_version="DELTA2-"$recent_base_version"."$recent_major_version"."$new_patch_version;
						echo "New release version: "$new_release_version;

						./utility/setprop.sh "recent_rc_branch" "${new_release_version}" "${release_version_property_file}"

					</shellscript>					
				</then>
			</if>

		<unset file="${release_version_property_file}"/>	
		<var name="recent_rc_branch" unset="true"/>
		<loadproperties  srcFile="${properties}/rc-version.properties"/>
		<property file="${release_version_property_file}"/>	


		<antcall target="create_branch">		
			<param name="new_rel_ver" value="${recent_rc_branch}"/>
		</antcall>


		<shellscript shell="bash">

			recent_release_version=`grep 'recent_rc_branch' "${release_version_property_file}" | cut -d '=' -f 2`;
			echo "Recent release version: "$recent_release_version;

			cd "${git_alpha2_checkout_root}"
			git checkout master
			git pull --rebase
			if [[ -z $(git ls-remote --heads --exit-code origin "${recent_release_version}") ]];
				then
					 echo "ALERT! created branch does not exist on remote. rc.version properties at jfrog will not be updated";
					 ./../utility/setprop.sh "Created_RC_Branch_Exists" "N" "${tmp_rc_branch_property_file}";
				else
					echo "created branch exist on remote";
					 ./../utility/setprop.sh "Created_RC_Branch_Exists" "Y" "${tmp_rc_branch_property_file}";
			fi

		</shellscript>

		<unset file="${tmp_rc_branch_property_file}"/>
		<var name="Created_RC_Branch_Exists" unset="true"/>
		<loadproperties  srcFile="${tmp}/rc.properties"/>
		<property file="${tmp_rc_branch_property_file}"/>

		<if>
			<equals arg1="${Created_RC_Branch_Exists}" arg2="Y" casesensitive="No"/>
				<then>
					<antcall target="upload_release-version_to_artifactory"/>
				</then>
		</if>			



		<echo message="======================================================== create_release_branch ===================================================="/>
		<echo message="==================================================================================================================================="/>

	</target>



	<target name="create_branch"> <!-- New -->
		<echo message="==================================================================================================================================="/>
		<echo message="===================================================== create_branch ==============================================================="/>
		<echo message=""/>			
		<echo message=""/>	

		<shellscript shell="bash">

				echo ""

				cd "${git_alpha2_checkout_root}"
				git checkout master
				git pull --rebase
				git checkout -b "${new_rel_ver}"			    
			    git push -u origin "${new_rel_ver}"
			    echo ""

		</shellscript>	

		<echo message=""/>				
		<echo message=""/>			
		<echo message="===================================================== create_branch ==============================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>	

	<target name="download_release-version_from_artifactory"> <!-- New -->
		<echo message="==================================================================================================================================="/>
		<echo message="========================================= download_release-version_from_artifactory ==============================================="/>		

		<loadproperties srcFile="${build_property_file}"/>

		<propertycopy name="artifactory_host" from="artifactory.rc-version.host"/>
		<propertycopy name="artifactory_uid" from="artifactory.username"/>
		<propertycopy name="artifactory_pwd" from="artifactory.password"/>		


		<antcall target="download_from_artifactory">			
			<param name="binary_file" value="rc-version.properties"/>
			<param name="artifactory_host" value="${artifactory.rc-version.host}"/>
			<param name="uid" value="${artifactory_uid}"/>
			<param name="pwd" value="${artifactory_pwd}"/>											
			<param name="download_path" value="${properties}"/>
		</antcall>

		<echo message="========================================= download_release-version_from_artifactory ==============================================="/>
		<echo message="==================================================================================================================================="/>
	</target>					

	<target name="upload_release-version_to_artifactory"> <!-- New -->
		<echo message="==================================================================================================================================="/>
		<echo message="=========================================== upload_release-version_to_artifactory ================================================="/>	

		<loadproperties srcFile="${build_property_file}"/>

		<propertycopy name="artifactory_host" from="artifactory.rc-version.host"/>
		<propertycopy name="artifactory_uid" from="artifactory.username"/>
		<propertycopy name="artifactory_pwd" from="artifactory.password"/>


		<antcall target="upload_to_artifactory">			
			<param name="binary_file" value="${properties}/rc-version.properties"/>
			<param name="destination" value="${artifactory.rc-version.host}"/>
			<param name="uid" value="${artifactory_uid}"/>
			<param name="pwd" value="${artifactory_pwd}"/>						
		</antcall>

		<antcall target="checkfile_at_artificatory">			
			<param name="binary_file" value="rc-version.properties"/>
			<param name="destination" value="${artifactory.rc-version.host}"/>
			<param name="uid" value="${artifactory_uid}"/>
			<param name="pwd" value="${artifactory_pwd}"/>						
		</antcall>

		<loadfile property="file" srcfile="${transit_point}/checkfile_at_artifactory_output.log"/>
		<propertyregex property="status_code" input="${file}" regexp="(HTTP\/1.1.)(\d+)" select="\2" override="true"/>
		<echo message="status_code: ${status_code}"/>

		<if>
			<equals arg1="${status_code}" arg2="200"/>
				<then>
					<echo message=""/>
					<shellscript shell="bash">
						touch "${artifactory_file_status_property_file}" 
						./utility/setprop.sh "release-version_available_at_artifactory" "0" "${artifactory_file_status_property_file}" <!-- New -->
					</shellscript>						
				</then>
			<else>
				<echo message="===================================== ! Alert ! =========================================="/>			
				<echo message="============ Error reading file availability. Require manual intervention ================"/>
				<echo message="===================================== ! Alert ! =========================================="/>	
				<shellscript shell="bash">
					touch "${artifactory_file_status_property_file}"
					./utility/setprop.sh "release-version_at_artifactory" "E" "${artifactory_file_status_property_file}" <!-- New -->
				</shellscript>										
			</else>
		</if>							

		<echo message="=========================================== upload_release-version_to_artifactory ================================================="/>
		<echo message="==================================================================================================================================="/>
	</target>
 

<!--**************************************** END OF TARGETS UNDER STUDY ****************************************************-->	

</project>

--------------------------------------------------------------------------------------------------------------------------

Docker

pipeline {

  agent {
    node {
        label 'robot-firefox710-ant'
        customWorkspace '/home/jenkins/workspace/a2-dev/A2_CM_INFRA/cm-workspace-dev'
         }
        }

    environment {
        JAVA_HOME = '/opt/tools/jdk1.8.0_181'
        PATH = "${PATH}:${JAVA_HOME}/bin"        
        //ANT_HOME = '/opt/apache-ant-1.10.9'
        //PATH = "${PATH}:${ANT_HOME}/bin:${JAVA_HOME}/bin"
                }        

    stages {
        
        stage('Initialize') { 
            steps { 
                    echo "$JAVA_HOME"
                    //echo "$ANT_HOME"
                    echo "$PATH"
                    sh "pwd"                    
                    sh "ant -version"                    
                    sh "java -version"
                    git branch: 'container', credentialsId: 'pothirendi.mounika', url: 'https://git.verso.sonera.fi/scm/a2/alpha2_cm_infra.git'
                    sh "ls -lat"                                              
                    sh "ls -a /home/jenkins/workspace/a2-dev/A2_CM_INFRA/cm-workspace-dev"                    
                    //sh "ant -Ddest=system setup_build_workspace"
                    //sh "ant -Ddest=system git_clone"

                  }
                            } 

        stage('Setup Build Workspace') { 
            steps { 
                    sh "ant setup_build_workspace"
                    sh "ls -a /home/jenkins/workspace/a2-dev/A2_CM_INFRA/cm-workspace-dev"
                  }            

            }
            
        stage('Clone A2 Repo') { 
            steps { 
                        sh "ant -Ddest=system git_clone"
                        sh "ls -a /home/jenkins/workspace/a2-dev/A2_CM_INFRA/cm-workspace-dev"
                  }            
            } 
            
            
        stage('Initialize Build Workspace') { 
            steps { 
                    echo "$JAVA_HOME"
                    //echo "$ANT_HOME"
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
                                  
       stage('create_binaries') { 
            steps { 
                    sh "pwd"
                    sh "ls -a /home/jenkins/workspace/a2-dev/A2_CM_INFRA/cm-workspace-dev"
                    sh "ls -a /home/jenkins/workspace/a2-dev/A2_CM_INFRA/cm-workspace-dev/tmp"
                    sh "cat /home/jenkins/workspace/a2-dev/A2_CM_INFRA/cm-workspace-dev/tmp/installation_to_include.properties"
                    sh "cat /home/jenkins/workspace/a2-dev/A2_CM_INFRA/cm-workspace-dev/tmp/affected_features_list.lst"
                    sh "cat /home/jenkins/workspace/a2-dev/A2_CM_INFRA/cm-workspace-dev/tmp/branch.properties"
                    sh "cat /home/jenkins/workspace/a2-dev/A2_CM_INFRA/cm-workspace-dev/tmp/temp.properties"
                  // sh "ant create_binaries"
                   
                  }
                                }                                                                           
                                          
            
            
                                              
   
                                               
                                             
      stage('deploy+') { 
            steps { 
                    sh "ant deploy+"
                  }
                                }   
                                                                                                                                                      

            
    } 
    }
-----------------------------------------------------------------------------
properties:::git.host=git.verso.sonera.fi/scm/a2/alpha2_ebs.git
git.username=alex.z.pandian
git.password=Welcome123
artifactory.username=alex.z.pandian
artifactory.password=Welcome123
artifactory.version.host=https://artifactory.verso.sonera.fi/ant_oracle-ebs_alpha2/version/
artifactory.rc-version.host=https://artifactory.verso.sonera.fi/ant_oracle-ebs_alpha2/rc-version/
artifactory.dev.host=https://artifactory.verso.sonera.fi/ant_oracle-ebs_alpha2/dev/
artifactory.test.host=https://artifactory.verso.sonera.fi/ant_oracle-ebs_alpha2/test/
artifactory.prod.host=https://artifactory.verso.sonera.fi/ant_oracle-ebs_alpha2/prod/
smtp.host=smtp.dave.sonera.fi
smtp.port=25

----------------------------------------------------------------
src.properties

env.1.hostname=a2uat
env.1.a2uat.hostaddress=fi004a2dev.ddc.teliasonera.net
env.1.a2uat.user=a2uata
env.1.a2uat.passwd=a2uat/a
env.1.a2uat.dbhostaddress=fi003a2dbdev.ddc.teliasonera.net
env.1.a2uat.dbuser=a2uato
env.1.a2uat.dbpasswd=a2uat/o
env.1.a2uat.dbport=1598
env.1.a2uat.dbservicename=A2UAT
env.1.a2uat.servnum=fa015
env.1.a2uat.appspasswd=appla2uat
env.1.a2uat.schemapasswd=fi004a2devA2UAT
env.1.a2uat.tomcatport=8377
env.1.a2uat.weblogicpasswd=weblogic1
env.1.a2uat.applicationurl=http://fi004a2dev.ddc.teliasonera.net:8077/OA_HTML/AppsLogin
env.1.a2uat.applicationuser=sysadmin
env.1.a2uat.applicationpasswd=admina2uat

