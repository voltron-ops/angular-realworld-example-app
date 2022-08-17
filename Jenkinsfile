import groovy.json.*
    
def extractAndUpdateVersion(fromFile, toFile){
    def json = readFile(file: "${fromFile}")
    def parsedJson = new JsonSlurperClassic().parseText(json)
    def currentVersion = parsedJson['version']
    json = readFile(file: "${toFile}")
    parsedJson = new JsonSlurperClassic().parseText(json)
    parsedJson['version'] = currentVersion
    def jsonOutput = JsonOutput.toJson(parsedJson)
    jsonOutput = JsonOutput.prettyPrint(jsonOutput)
    writeFile(file: "${toFile}", text: "${jsonOutput}")
}

def getVersion(filename){
    def json = readFile(file: "${filename}")
    def parsedJson = new JsonSlurperClassic().parseText(json)
    def currentVersion = parsedJson['version']
    return currentVersion
}

node{
    stage("Clean Workspace"){
        cleanWs()
    }
    stage("Checkout SCM"){
        git branch: 'test-qa', url: 'https://github.com/voltron-ops/angular-realworld-example-app.git'
    }
    stage("UpdateVersion"){
        extractAndUpdateVersion("package.json", "app_version-dev.json" )
        bat "type app_version-dev.json"
    }
    stage("GitPush"){
        def newVersion = getVersion("app_version-dev.json")
        bat "git config --global user.name \"voltron-ops\""
        bat "git config --global user.email \"kashifofme@gmail.com\""
        bat "git add app_version-dev.json"
        bat "git commit -m \"Version updated after successfull build\""
        bat "git tag ${newVersion}-testqa"
        withCredentials([usernamePassword(credentialsId: 'gitCreds', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
            bat "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/voltron-ops/angular-realworld-example-app.git HEAD:test-qa"
            bat "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/voltron-ops/angular-realworld-example-app.git --tags"
        }
    }
}
