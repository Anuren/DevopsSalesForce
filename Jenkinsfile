#!groovy
import groovy.json.JsonSlurperClassic
node {

    def BUILD_NUMBER=env.BUILD_NUMBER
    def RUN_ARTIFACT_DIR="tests/${BUILD_NUMBER}"
    def SFDC_USERNAME

    def HUB_ORG= test-xsp28kqlnm0v
    def SFDC_HOST = env.SFDC_HOST_DH
    def JWT_KEY_CRED_ID = env.JWT_CRED_ID_DH
    def CONNECTED_APP_CONSUMER_KEY=3MVG9z6NAroNkeMkqBqT_Ng0I3RRMAQ0y7dB.CWblVCDDZevEvbPq4tuf3M0D9laUmvT5WLXSl1_R2mBgs3fs

    println 'KEY IS' 
    println JWT_KEY_CRED_ID
    println HUB_ORG
    println SFDC_HOST
    println CONNECTED_APP_CONSUMER_KEY
    def toolbelt = tool 'toolbelt'

    stage('checkout source') {
        // when running in multi-branch job, one must issue this command
        checkout scm
    }

    withCredentials([file(credentialsId: JWT_KEY_CRED_ID)]) {
        stage('Deploye Code') {
            if (isUnix()) {
                rc = sh returnStatus: true, script: "${toolbelt} force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${jwt_key} --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            }else{
                 rc = bat returnStatus: true, script: "\"${toolbelt}\" force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile \"${jwt_key}\" --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            }
            if (rc != 0) { error 'hub org authorization failed' }

			println rc
			
			// need to pull out assigned username
			if (isUnix()) {

						   //rmsg = sh returnStdout: true, script: "${toolbelt} force:mdapi:deploy -d DevSandbox/. -u ${HUB_ORG}"
						   rmsg = sh "sfdx force:mdapi:deploy -d output --targetusername $HUB_ORG --wait 20"
							println 'Deploy succes'

	}else{                                                                       
         rr = bat returnStatus: true, script: "\"${toolbelt}\" force:auth:logout --username ${HUB_ORG}"  
		 rc = bat returnStatus: true, script: "\"${toolbelt}\" force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile \"${jwt_key}\" --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
		 rmsg = bat returnStdout: true, script: "\"${toolbelt}\" force:source:deploy -x manifest/package.xml -u ${HUB_ORG}  --wait 20"

	}        
    
			if (isUnix()) {
				rmsg = sh returnStdout: true, script: "${toolbelt} force:mdapi:deploy -d manifest/. -u ${HUB_ORG}"
			}else{
			   rmsg = bat returnStdout: true, script: "\"${toolbelt}\" force:mdapi:deploy -d manifest/. -u ${HUB_ORG}"
			}
			  
            printf rmsg
            println('Hello from a Job DSL script!')
            println(rmsg)
        }
    }
}
