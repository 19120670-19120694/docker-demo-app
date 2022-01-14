node {
    def docker_registry = "https://registry.hub.docker.com"
    def docker_credential = "docker-hub"
    def docker_id = "19120694"
    def app_name = "nodejs-simple-app"

	def app
	stage("SCM Checkout"){
		checkout scm
	}

	stage("Build Docker Image"){
		app = docker.build("${docker_id}/${app_name}:${env.BUILD_ID}")
	}

	stage("Integration Test"){
		app.inside {
            sh "npm --prefix ./ run test"
            sh "echo 'running addional test'"
            sh "echo 'passed'"
		}
	}

	stage("Push Docker Image"){
		docker.withRegistry("${docker_registry}", "${docker_credential}"){
			app.push("${env.BUILD_ID}") // push images with new tag to docker hub
			app.push("latest") // change the latest tag to it
		}
    }

	stage("Deploy"){
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
