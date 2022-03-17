node {
    def server = Artifactory.server "jfrogeval"
    def rtPip = Artifactory.newPipBuild()
    def buildInfo
    def virtual_env_activation = "./venv/bin/activate" // pip virtual-environment activation command
  
    stage ('Artifactory configuration') {
        rtPip.resolver repo: 'py-virtual', server: server
        buildInfo = Artifactory.newBuildInfo()
    }

    stage ('Pip install') {
        sh "python3 -m venv ."
        rtPip.install buildInfo: buildInfo, args: "-r ./requirements.txt", envActivation: virtual_env_activation
    }

    stage ('Package and create distribution archives') {
        sh '''
            $virtual_env_activation
            python3 setup.py sdist bdist_wheel
        '''
    }

    stage ('Upload packages') {
        def uploadSpec = """{
            "files": [
                {
                    "pattern": "./dist/",
                    "target": "py-virtual/"
                }
            ]
        }"""
        server.upload buildInfo: buildInfo, spec: uploadSpec
    }

    stage ('Publish build info') {
        server.publishBuildInfo buildInfo
    }
}
