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
        checkout scm
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
        bat "git push https://voltron-ops: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@github.com/voltron-ops/angular-realworld-example-app"
        bat "git push https://voltron-ops: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@github.com/voltron-ops/angular-realworld-example-app --tags"
    }
}
