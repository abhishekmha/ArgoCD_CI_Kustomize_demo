pipeline{
    agent any
    environment { 
        registry = "gcr.io/cybage-devops/spring-boot-hello-world" 
        registryCredential = 'jenkins-argo-demo Google Container Registry Account' 
        dockerImage = '' 

    }
    
    tools{
        jdk 'java_home'
        maven 'maven_home'
    }
    
    stages{
        stage('git fetch'){
            steps{
                git branch: 'master', url: 'https://github.com/abhishekmha/ArgoCD_CI_Kustomize.git'        
            }
        
        }
        stage('build'){
            steps{
                sh 'mvn clean install'
            }
        }
        stage('docker build'){
            steps{
                script { 
                    dockerImage = docker.build registry + ":$BUILD_NUMBER" 
                }
            }
        }
        stage('docker push'){
            steps{
                withCredentials([file(credentialsId: 'gcr_creds', variable: 'GC_KEY')]){
                    sh 'cat key.json | docker login -u _json_key --password-stdin https://gcr.io'
                    sh 'docker push gcr.io/cybage-devops/spring-boot-hello-world:$BUILD_NUMBER'
                }
                
            }
        }
        
        stage('deploy e2e'){
             steps{
                git branch: 'master', url: 'https://github.com/abhishekmha/ArgoCD_CD_ShellScript.git'        
            
                //sh 'rm -rf ArgoCD_CD'
		    //sh 'git clone https://${git_username}:${git_password}@github.com/abhishekmha/ArgoCD_CD_ShellScript.git'
		
                dir("ArgoCD_CD_ShellScript"){
	            sh '''
		      cd ./yamls 
                      image_name=`grep -i image deployment.yaml`
		      build_number=`echo $image_name| rev | cut -d":" -f1  | rev`
		      sed -i "s/$build_number/${BUILD_NUMBER}/g" deployment.yaml
		      git add .
		      git commit -m "published new version ${BUILD_NUMBER}"
		      git push https://${git_username}:${git_password}@github.com/abhishekmha/ArgoCD_CD_ShellScript.git master
		      
		    '''
		}
                    //sh "cd ./yamls && sed -i 's/world:*/world:34/g'"
                    //sh "git commit -am 'Publish new version' && git push || echo 'no changes'"
                }
            }
        } 
                
    }
