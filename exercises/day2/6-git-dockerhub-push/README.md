### **Jenkins Pipeline Exercise: Building and Pushing a Docker Container to Docker Hub**

#### **Objective**
Learn how to create a Jenkins pipeline that:

1. Uses Docker Hub credentials securely.
2. Builds a Docker container from a GitHub repository containing a `Dockerfile` and `index.html`.
3. Tags the Docker image.
4. Pushes the Docker image to Docker Hub.
5. Utilizes Jenkins built-in environment variables.

#### **Prerequisites**

- **Jenkins Installation**: Ensure Jenkins is installed and running.
- **Docker Installed on Jenkins Server**: Jenkins must have Docker installed and have permission to run Docker commands.
- **Docker Hub Account**: Create a Docker Hub account if you don’t have one.
- **GitHub Account**: Students should have access to GitHub to create repositories.
- **Basic Knowledge**: Familiarity with Jenkins, Docker, GitHub, and basic command-line operations.

#### **Exercise Steps**

1. **Create a GitHub Repository**
2. **Create Necessary Files (`Dockerfile` and `index.html`)**
3. **Set Up Jenkins Credentials**
4. **Create Jenkins Pipeline**
5. **Run the Pipeline and Verify**

---

### **Step 1: Create a GitHub Repository**

1. **Log in to GitHub**: Go to [GitHub](https://github.com/) and log in to your account.

2. **Create a New Repository**:
   - Click on the **+** icon in the top-right corner and select **New repository**.
   - **Repository Name**: `apache-webserver`
   - **Description**: `A simple Apache webserver with custom index.html`
   - **Public** or **Private**: Choose based on preference.
   - **Initialize Repository**: Do **not** initialize with a README, `.gitignore`, or license.
   - Click **Create repository**.

3. **Clone the Repository Locally**:
   ```bash
   git clone https://github.com/your-username/apache-webserver.git
   cd apache-webserver
   ```

---

### **Step 2: Create Necessary Files**

1. **Create `index.html`**:
   ```html
   <!-- index.html -->
   <!DOCTYPE html>
   <html>
   <head>
       <title>Welcome to Apache</title>
   </head>
   <body>
       <h1>Hello from Jenkins Pipeline!</h1>
       <p>This is a sample Apache webserver.</p>
   </body>
   </html>
   ```

2. **Create `Dockerfile`**:
   ```Dockerfile
   # Dockerfile
   FROM httpd:2.4

   # Remove the default index.html
   RUN rm -rf /usr/local/apache2/htdocs/*

   # Copy custom index.html to Apache's document root
   COPY index.html /usr/local/apache2/htdocs/

   # Expose port 80
   EXPOSE 80
   ```

3. **Commit and Push Changes**:
   ```bash
   git add Dockerfile index.html
   git commit -m "Add Dockerfile and index.html"
   git push origin main
   ```

---

### **Step 3: Set Up Jenkins Credentials**

1. **Access Jenkins Dashboard**: Navigate to your Jenkins instance (e.g., `http://localhost:8080`).

2. **Add Docker Hub Credentials**:
   - Click on **Credentials** in the sidebar.
   - Select **System** and then **Global credentials (unrestricted)**.
   - Click **Add Credentials**.
   - **Kind**: `Username with password`
   - **Scope**: `Global`
   - **Username**: Your Docker Hub username.
   - **Password**: Your Docker Hub password.
   - **ID**: `dockerhub-credentials` (This ID will be used in the pipeline)
   - **Description**: `Docker Hub Credentials for pushing images`
   - Click **OK**.

---

### **Step 4: Create Jenkins Pipeline**

1. **Create a New Pipeline Job**:
   - From the Jenkins dashboard, click **New Item**.
   - **Name**: `Build-and-Push-Apache-Webserver`
   - **Type**: `Pipeline`
   - Click **OK**.

2. **Configure Pipeline**:
   - **Description**: `Pipeline to build Docker image from GitHub repo and push to Docker Hub.`
   - **Pipeline Definition**: Select **Pipeline script**.

3. **Jenkinsfile Script**:
   Paste the following pipeline script into the **Pipeline script** text area.

```groovy
pipeline {
    agent any

    environment {
        GIT_REPO = 'https://github.com/stv707/apache-webserver.git'
        IMAGE_NAME = 'stv707/apache-webserver'
        TAG = "${env.BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                git url: "${env.GIT_REPO}", branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${env.IMAGE_NAME}:${env.TAG}")
                    dockerImage.tag('latest')
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-credentials') {
                        dockerImage.push()
                        dockerImage.push('latest')
                    }
                }
            }
        }

        stage('Stop and Remove Existing Container') {
            steps {
                script {
                    def containerName = 'apache-webserver'
            
            // Check if the container is running
                    def isRunning = sh(script: "docker ps -q --filter 'name=${containerName}'", returnStatus: true) == 0
            
            // Stop and remove the container if it exists, else print a warning
                    if (isRunning) {
                        sh "docker stop ${containerName} || echo 'Container not running'"
                        sh "docker rm ${containerName} || echo 'Container not found'"
                    } else {
                        echo "No running container named ${containerName} to stop."
            }
        }
    }
}


        stage('Run New Container') {
            steps {
                script {
                    def containerName = 'apache-webserver'
                    sh "docker run -d --name ${containerName} -p 80:80 ${env.IMAGE_NAME}:${env.TAG}"
                }
            }
        }

       stage('Cleanup') {
             steps {
                 script {
                      // Only remove the specific build tag, not the 'latest' tag to prevent issues
                     sh "docker rmi ${env.IMAGE_NAME}:${env.TAG} || echo 'Image not found or is in use'"
            
                    // Verify if 'latest' tag is used by running containers before removing
                    def inUse = sh(script: "docker ps --filter 'ancestor=${env.IMAGE_NAME}:latest' -q", returnStatus: true) == 0
                    if (!inUse) {
                        sh "docker images -q ${env.IMAGE_NAME}:latest && docker rmi ${env.IMAGE_NAME}:latest || echo 'No latest image to remove'"
                    } else {
                        echo "'latest' tag is in use, skipping image removal for 'latest'"
            }
        }
    }
}
    }

    post {
        success {
            echo "Docker image successfully built, pushed, and container is running."
        }
        failure {
            echo "Pipeline failed. Please check the logs."
        }
    }
}
```

   **Notes**:
   - Replace `your-username` in `GIT_REPO` with your actual GitHub username.
   - Replace `your-dockerhub-username` in `IMAGE_NAME` with your Docker Hub username.
   - The `TAG` uses Jenkins' built-in `BUILD_NUMBER` variable to version the Docker image.

4. **Save the Pipeline**: Click **Save**.

---

### **Step 5: Run the Pipeline and Verify**

1. **Trigger the Pipeline**:
   - From the pipeline’s page, click **Build Now**.
   - The build will start and execute each stage sequentially.

2. **Monitor the Pipeline**:
   - Click on the build number (e.g., `#1`) under **Build History**.
   - Click **Console Output** to view real-time logs and ensure each stage completes successfully.

3. **Verify Docker Image on Docker Hub**:
   - Log in to your Docker Hub account.
   - Navigate to **Repositories** and locate `apache-webserver`.
   - You should see the newly pushed image with the corresponding `BUILD_NUMBER` tag and `latest` tag.

4. **Test the Docker Image Locally (Optional)**:
   ```bash
   docker pull your-dockerhub-username/apache-webserver:latest
   docker run -d -p 8080:80 your-dockerhub-username/apache-webserver:latest
   ```
   - Open a web browser and go to `http://localhost:8080` to see the Apache webserver with the custom `index.html`.

---

### **Using Jenkins Built-in Environment Variables**

In the provided `Jenkinsfile`, several built-in environment variables are utilized:

- `env.BUILD_NUMBER`: The current build number, used for tagging the Docker image.
- `env.GIT_COMMIT`: The commit hash of the current build (can be used for more detailed tagging if desired).
- `env.JOB_NAME`: The name of the Jenkins job.
- `env.WORKSPACE`: The workspace directory where the pipeline is running.

**Example Usage**:

You can incorporate additional environment variables as needed. For instance, to include the Git commit hash in the Docker tag:

```groovy
environment {
    GIT_REPO = 'https://github.com/your-username/apache-webserver.git'
    IMAGE_NAME = 'your-dockerhub-username/apache-webserver'
    TAG = "${env.BUILD_NUMBER}-${env.GIT_COMMIT}"
}
```

---