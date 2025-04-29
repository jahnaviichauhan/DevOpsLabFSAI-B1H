# DevOps Lab 9 – Build using Jenkins

**Name:** Jahnavi Singh Chauhan
**SAP ID:** 500102342
**Roll Number:** R2142220526  
**Program:** B.Tech CSE (Full Stack AI) – Honours  
**College:** UPES Dehradun  
**Lab Title:** Jenkins-based CI/CD for Dockerized FastAPI Application  
**Repository:** [https://github.com/jahnavichauhan/fastapi-dockerize-jenkins-lab]

---

## Objective

To automate the CI/CD process using Jenkins for a Python FastAPI application by building a Docker image and pushing it to Docker Hub upon each code push or manual trigger.

---

## Tools & Technologies Used

- Jenkins (Self-hosted CI/CD server)
- Docker (Container platform)
- Docker Hub (Image registry)
- FastAPI (Python web framework)
- Git (Version control)

---

## FastAPI App Code (`main.py`)

```python
from fastapi import FastAPI
import uvicorn

app = FastAPI()

@app.get("/")
def root():
    return {"name": "Jaanhvi", "Location": "Dehradun"}

@app.get("/{data}")
def dynamic(data: str):
    return {"hi": data, "Location": "Dehradun"}

if __name__ == "__main__":
    uvicorn.run("main:app", host="0.0.0.0", port=80, reload=True)
```

---

## Dockerfile

```dockerfile
FROM ubuntu

RUN apt update -y && apt install -y python3 python3-pip pipenv

WORKDIR /app
COPY . /app/
RUN pipenv install -r requirements.txt

EXPOSE 80
CMD pipenv run python3 ./main.py
```

---

## Jenkins Configuration Steps

### 1. Create a Pipeline Job

- Go to Jenkins Dashboard → New Item → Select **Pipeline** → Name: `FastAPI Docker Pipeline`

### 2. Configure DockerHub Credentials

- Go to Jenkins → **Manage Jenkins** → **Credentials**
- Add new credentials:
  - Type: Username with password
  - ID: `dockerhub-creds`
  - Username: Your Docker Hub username
  - Password: Docker Hub access token

### 3. Jenkinsfile (Pipeline Script)

```groovy
pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/your-username/your-repo.git'
            }
        }

        stage('Docker Login') {
            steps {
                sh "echo ${DOCKERHUB_CREDENTIALS_PSW} | docker login -u ${DOCKERHUB_CREDENTIALS_USR} --password-stdin"
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t yourdockerhubusername/fast_api_dockerize .'
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'docker push yourdockerhubusername/fast_api_dockerize'
            }
        }
    }
}
```

---

## Output / Result

- Jenkins checks out the source code from GitHub.
- Logs into Docker Hub using saved credentials.
- Builds the Docker image.
- Pushes the image to Docker Hub.

---

## Conclusion

This lab demonstrates how Jenkins can be used to set up a CI/CD pipeline for Dockerized applications. By using Jenkins with Docker, we automate the build and deployment workflow of a FastAPI application in a reproducible manner.
