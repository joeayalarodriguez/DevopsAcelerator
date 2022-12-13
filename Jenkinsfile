!groovy
import groovy.json.JsonSlurperClassic
node {
    def SFDC_USERNAME="joeayala@sfdx.com" //Hay que añadir aquí el usuario que tengamos conectado
    def SFDC_HOST = "https://login.salesforce.com"
    def CONNECTED_APP_CONSUMER_KEY =""//Cambiar por el Consumer Key generado en la Parte II
    def JWT_KEY_CRED_ID = "3MVG9ux34Ig8G5erxLhTjS4clVMbYDeHuldiaPSF4wDiMUgCf.okCeCzQuedfqarp0mDz8qoUg42NN9fyMYVq"//Cambiar por las credenciales de Jenkins creadas en la Parte II
    def sfdx = tool '555739f3-d431-4d1c-8f73-b5885cde472d'//Creamos una variable para usar sfdx a partir de la Custom Tool que creamos en la Parte I

    stage('Check branch name') { // Primer paso para compobar que estamos en la rama correcta
        println env.BRANCH_NAME
        if(env.BRANCH_NAME=="master"){// Este nombre es el que le demos al seleccionar el repositorio dentro del Pipeline
            println('Script from master!')
            println sfdx
        }
        else{
            println sfdx
            error 'Incorrect branch' 
        }
        checkout scm
    }

    withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')]) {        
        stage('Deployment') {
            if (isUnix()) {//Para sistemas Unix el comando varía un poco el formato
                rc = sh returnStatus: true, script: "${sfdx} force:auth:logout --targetusername ${SFDC_USERNAME} -p" //Hacemos logout para evitar un error
				// Autorizamos la dev hub org
                rc = sh returnStatus: true, script: "${sfdx} force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${SFDC_USERNAME} --jwtkeyfile ${jwt_key_file} --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            }else{//ejecutamos lo mismo para sistemas Windows
                rc = sh returnStatus: true, script:"\"${sfdx}\" force:auth:logout --targetusername ${SFDC_USERNAME} -p"
                rc = bat returnStatus: true, script: "\"${sfdx}\" force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${SFDC_USERNAME} --jwtkeyfile \"${jwt_key_file}\" --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            }
            if (rc != 0) { error 'Org authorization has failed' }

			println rc
			//Realizamos el despliegue de todo force-app
			if (isUnix()) {
                rmsg = sh returnStdout: true, script: "${sfdx} force:source:deploy --sourcepath force-app -u ${SFDC_USERNAME}"
			}else{
               rmsg = bat returnStdout: true, script: "\"${sfdx}\" force:source:deploy --sourcepath force-app -u ${SFDC_USERNAME}"
			}
			  
            printf rmsg
            println(rmsg)
        }
    }
}