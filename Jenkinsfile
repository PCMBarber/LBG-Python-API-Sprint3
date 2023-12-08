pipeline {
    agent any
    stages {
        stage('Build Flask App') {
            steps {
                script {
			        if (env.GIT_BRANCH == 'origin/main') {
                        sh '''
                        echo "Build not required in main"
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                        docker build -t eu.gcr.io/lbg-mea-14/piers-flask-demo:latest -t eu.gcr.io/lbg-mea-14/piers-flask-demo:v$BUILD_NUMBER .
                        '''
                    } else {
                        sh '''
                        echo "Unrecognised branch"
                        '''
                    }
		        }
           }
        }
        stage('Push Images') {
            steps {
                script {
			        if (env.GIT_BRANCH == 'origin/main') {
                        sh '''
                        echo "Push not required in main"
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                        docker push eu.gcr.io/lbg-mea-14/piers-flask-demo:latest
                        docker push eu.gcr.io/lbg-mea-14/piers-flask-demo:v$BUILD_NUMBER
                        '''
                    } else {
                        sh '''
                        echo "Unrecognised branch"
                        '''
                    }
                }
            }
        }
        stage('Clean Up') {
            steps {
                script {
                    if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                        docker rmi eu.gcr.io/lbg-mea-14/piers-flask-demo:latest
                        docker rmi eu.gcr.io/lbg-mea-14/piers-flask-demo:v$BUILD_NUMBER
                        '''
                    } else if (env.GIT_BRANCH == 'origin/main') {
                        sh '''
                        echo "rmi not required in main"
                        '''
                    } else {
                        sh '''
                        echo "Unrecognised branch"
                        '''
                    }
                }
            } 
        }
        stage('Deploy') {
            steps {
                script {
			         if (env.GIT_BRANCH == 'origin/main') {
                        sh '''
                        kubectl apply -f ./kubernetes -n production
                        kubectl rollout restart deployment flask-deployment -n production
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                        kubectl apply -f ./kubernetes -n development
                        kubectl rollout restart deployment flask-deployment -n development
                        '''
                    } else {
                        sh '''
                        echo "Unrecognised branch"
                        '''
                    }
		        }
            }
        }
    }
}
