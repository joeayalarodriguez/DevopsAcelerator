
import groovy.json.JsonSlurperClassic
node {

    def BUILD_NUMBER=env.BUILD_NUMBER
    def RUN_ARTIFACT_DIR="tests/${BUILD_NUMBER}"
    def SFDC_USERNAME

    def HUB_ORG='joeayala@sfdx.com'
    def SFDC_HOST = "https://login.salesforce.com"
    def JWT_KEY_CRED_ID = "555739f3-d431-4d1c-8f73-b5885cde472d"
    def CONNECTED_APP_CONSUMER_KEY="3MVG9ux34Ig8G5erxLhTjS4clVMbYDeHuldiaPSF4wDiMUgCf.okCeCzQuedfqarp0mDz8qoUg42NN9fyMYVq"

    println 'Validando credenciales de conexión ...' 
    println JWT_KEY_CRED_ID
    println HUB_ORG
    println SFDC_HOST
    println CONNECTED_APP_CONSUMER_KEY
    def toolbelt = tool 'toolbelt'

    stage('checkout source') {
        // when running in multi-branch job, one must issue this command
        checkout scm
        println('Stage 1')
    }

    withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')]) {
        println 'Validando credenciales de conexión segunda parte ...' 
        println JWT_KEY_CRED_ID
        stage('Deploye Code') {
            if (isUnix()) {
                 println 'autenticando unix...' 
                rc = sh returnStatus: true, script: "${toolbelt} force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile /home/joe/opensslw/server.key --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            }else{
                 println 'autenticando ...' 
                 rc = bat returnStatus: true, script: "\"${toolbelt}\" force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile /home/joe/opensslw/server.key --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            }
            if (rc != 0) { error 'hub org authorization failed' }

			println rc
			
			// need to pull out assigned username
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