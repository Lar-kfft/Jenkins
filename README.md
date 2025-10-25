# Jenkins Pipeline Examples

## Вариант 1: Простой пайплайн

```groovy
pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/Lar-kfft/Jenkins.git'
            }
        }
        
        stage('Build') {
            steps {
                sh 'mvn clean compile'
            }
        }
        
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
    }
}
```
## Вариант 2: Docker пайплайн

```groovy
pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = 'my-app'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${DOCKER_IMAGE}:${env.BUILD_NUMBER}")
                }
            }
        }
        
        stage('Test Container') {
            steps {
                sh "docker run --rm ${DOCKER_IMAGE}:${env.BUILD_NUMBER} echo 'Test completed'"
            }
        }
    }
}
```

## Вариант 3: Мульти-веточный пайплайн

```groovy
pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        
        stage('Tests') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        sh 'npm run test:all'
                    } else {
                        sh 'npm test'
                    }
                }
            }
        }
        
        stage('Deploy') {
            when {
                branch 'main'
            }
            steps {
                sh 'echo "Deploying to production..."'
            }
        }
    }
}
```

