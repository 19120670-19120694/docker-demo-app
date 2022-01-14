node {
    def docker_registry = "https://registry.hub.docker.com"
    def docker_credential = "docker"
    def docker_id = "19120694"
    def app_name = "nodejs-simple-app"

	def app
	stage("clone"){
		checkout scm
	}

	stage("build"){
		app = docker.build("${docker_id}/${app_name}:${env.BUILD_ID}")
	}

	stage("test"){
		app.inside {
            sh "npm --prefix ./ run test"
            sh "echo 'running addional test'"
            sh "echo 'passed'"
		}
	}

	stage("push"){
		docker.withRegistry("${docker_registry}", "${docker_credential}"){
			app.push("${env.BUILD_ID}") // push images with new tag to docker hub
			app.push("latest") // change the latest tag to it
		}
    }

	stage("deploy"){
        try {
            // kill app_name if it is running
            sh "echo Kill ${app_name} if it is running"
            sh "docker kill ${app_name}"
        }
        catch (exe){}
        finally {
            app.run("--rm --name ${app_name} -p 3000:3000")
        }
	}
}
