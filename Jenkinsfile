pipeline{
    agent any
    // triggers { cron('* * * * *') }
    parameters{
        booleanParam(
            name: 'IAC_ENABLED', 
            defaultValue: false,
            description: 'Is infraestructure as code enabled?')
        booleanParam(
            name: 'BG_DEPLOYMENT',
            defaultValue: false,
            description: 'Is blue green deployment enabled?')
    }
    stages
    {
    	stage("Checkout"){
    	    options {
    	        timeout(time:1)
    	    }
    	    steps{
				echo("Checkout")
                git credentialsId: 'GIT_TOKEN', url: 'https://github.com/jaime-oviedo/aurea-tests.git'
				// sleep(65) //we make it timeout
    	    }
    	}
        stage("Compile"){
			steps{
				sh "./gradlew compileJava"			    
			}
		}
		stage("Unit tests"){
		    steps{
		        sh "./gradlew test"
		    }
		}
		stage("Code coverage"){
		    steps{
		    	sh "./gradlew jacocoTestReport"
		    	publishHTML (target: [
               		reportDir: 'build/reports/jacoco/test/html',
               		reportFiles: 'index.html',
               		reportName: "JaCoCo Report"
          		])
		    	sh "./gradlew jacocoTestCoverageVerification"
		    }
		}
		stage("Static code analysis"){
			steps{
				sh "./gradlew checkstyleMain"
				publishHTML (target: [
     				reportDir: 'build/reports/checkstyle/',
     				reportFiles: 'main.html',
     				reportName: "Checkstyle Report"
				])
			}
		}
		stage("Package"){
			steps{
			    echo("Generate binaries for Nexus and for creating the docker image")		    
			}
		}
		stage("Nexus push"){
		    steps{
		        echo("push binaries to Nexus")
		    }   
		}
		stage("Docker build"){
			steps{
		        echo("Generate docker image")
			}
		}
		stage("Docker push"){
			steps{
		    	echo("Push docker image to registry")
			}
		}
		stage("Functional QA env provisioning"){
			steps{
				echo("Provision the staging environmnet using AWS CDK and Ansible")
			}
		}
		stage("Deploy to QA"){
			steps{
             	echo("deploy containers to the newly provisioned QA environments")
             	echo("for databases, either (preferred) deploy a container containing synthetic data,")
             	echo("or instantiate a db container and run synthentic data provisioning scrupts.")
         	}		                  
		}
		stage("Functional tests"){
		    steps{
		     	echo("Execute functional tests")
		     	echo("Publish results")
		    }
		}
		stage("Functiona QA env deprovisioning"){
			steps{
			    echo("Remove the provisioned hardware")
		    }
		}
		stage("Stage env provisioning"){
			steps{
				echo("Provision the staging environmnet using AWS CDK and Ansible")
				echo("This environment has a topology architecturally-equivalent to production")
				echo("By architecturally-equivalent I mean that you can extrapolate the quality attributes")
				echo("manifested by the system in this env to Prod.")
			}
		}
		stage("Deploy to Stage"){
			steps{
             	echo("/deploy containers to the newly provisioned Stage environment")
             	echo("for databases, either (preferred) deploy a container containing synthetic data, or instantiate a db container and run synthentic data provisioning scrupts.")
         	}		                  
		}
		stage("Performance tests"){
		    steps{
		     	echo("Execute performance tests")
		    }
		}
		stage("Security tests infrastructure deployment"){
			steps{
				echo("Drop and recreate the containers")
			}
		}
		stage("Security tests"){
		    steps{
		     	echo("Execute security tests")
		     	echo("Publish results")
		    }
		}
		stage("Acceptance tests infrastructure"){
			steps{
				echo("Drop and recreate the containers")
			}
		}
		stage("Run acceptance tests"){
		    steps{
		     	echo("Some day, (I hope) we'll use BDD to write executable acceptance tests instead of writing text based E2E")
		    }
		}
		stage("Stage env deprovisioning"){
			steps{
				echo("Remove provisioned hardware")
				echo("Publish results")
			}
		}
		stage("Production deployment"){
			steps{
				echo("Perform blue green deployment")
			}
		}
    }
	post{
        always{
            echo("Clean")
	    }
	}
}
