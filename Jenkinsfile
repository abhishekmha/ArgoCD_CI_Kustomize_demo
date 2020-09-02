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
                git branch: 'master', url: 'https://github.com/abhishekmha/ArgoCD_CD_Kustomize.git'       
                 
                 sh '''
                    cd ./e2e
                    kustomize edit set image gcr.io/cybage-devops/spring-boot-hello-world=gcr.io/cybage-devops/spring-boot-hello-world:$BUILD_NUMBER
                    git add .
                    git commit -m "published new version ${BUILD_NUMBER}"
                    git push https://${git_username}:${git_password}@github.com/abhishekmha/ArgoCD_CD_Kustomize.git master
                    
                 '''
            
               
                }
            }
        } 
                
    }
