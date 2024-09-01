pipeline 
{
    agent any

    parameters 
    {
        choice(name: 'DATABASE_TYPE', choices: ['SQL', 'MONGO'], description: 'Choose the primary database to use')
    }

    environment 
    {
        DOCKERHUB_CREDENTIALS = credentials('lanirelad')
        // option to make the app more parameterized - additional changes should be made respectively to the docker-compose.yaml
        // MYSQL_HOST = 'dbSql'
        // MYSQL_PORT = '3306'
        // MONGO_URI = 'mongodb://mongo:27017/'
    }

    stages 
    {
        stage('Clone Repository') 
        {
            steps 
            {
                git branch: 'main', url: 'https://github.com/lanirelad/CICD-JenPipe'
            }
        }

        stage('Build Docker Image') 
        {
            steps 
            {
                script 
                {
                     def image = docker.build("${DOCKERHUB_CREDENTIALS_USR}/contacts-manager:latest")
                }
            }
        }

        stage('Push to DockerHub') 
        {
            steps 
            {
                script 
                {
                    docker.withRegistry('https://registry.hub.docker.com', 'lanirelad') 
                    {
                        docker.image("${DOCKERHUB_CREDENTIALS_USR}/contacts-manager:latest").push()
                    }
                }
            }
        }

        stage('Deploy with Docker Compose') 
        {
            steps 
            {
                script 
                {
                    if (params.DATABASE_TYPE == 'SQL') 
                    {
                        // Set environment variables for MySQL
                        env.DB_USAGE = 'SQL'
                        // env.DB_HOST = MYSQL_HOST
                    } 
                    else if (params.DATABASE_TYPE == 'MONGO') 
                    {
                        // Set environment variables for MongoDB
                        env.DB_USAGE = 'MONGO'
                        // env.MONGO_URI = MONGO_URI
                    }

                    // Run Docker Compose
                    sh 'docker-compose up -d'
                }
            }
        }
    }

}