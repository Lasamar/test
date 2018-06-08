#!groovy
def configFolder = params.CONFIG_FOLDER as String
def applicationName = params.APPLICATION_NAME as String
def gitUrl = params.GIT_URL as String
def gitBranch = params.GIT_BRANCH as String
def ldapCredentials = params.LDAP_CREDENTIAL as String


node {
    stage('INIT') {
        sh 'rm -rf ./*'
    }
    stage('CHECK PARAMETERS') {
        if ((applicationName == null) || applicationName.isEmpty()) {
            error 'Il parametro applicationName è null o non è valorizzato!'
        }
        if ((gitUrl == null) || gitUrl.isEmpty()) {
            error 'Il parametro gitUrl è null o non è valorizzato!'
        }
        if ((configFolder == null) || configFolder.isEmpty()) {
            error 'Il parametro configFolder è null o non è valorizzato!'
        }
        if ((ldapCredentials == null) || ldapCredentials.isEmpty()) {
            error 'Il parametro ldapCredentials è null o non è valorizzato!'
        }
    }
    stage('CHECKOUT PROJECT') {
        checkout([$class : 'GitSCM', branches: [[name: gitBranch]], doGenerateSubmoduleConfigurations:
                false, extensions: [],
                  submoduleCfg: [], userRemoteConfigs: [[credentialsId: ldapCredentials, url: gitUrl]]])
    }
    stage('DELETE & RECREATE CONFIGMAP') {
        sh "oc delete cm $applicationName"
        sh "oc create cm $applicationName --from-file=$configFolder"
        sh "oc label cm $applicationName app=$applicationName"
        sh "oc rollout latest $applicationName"
    }
}
