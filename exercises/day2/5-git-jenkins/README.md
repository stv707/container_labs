# Exercise 5: Git Build Container using Git 

In this exercise, we'll learn to build a container from Git Repo and Run that container

Here’s a step-by-step guide to creating a Jenkins job that will build a container from the Dockerfile in your `app-x` repo and run it while exposing port 3000.

---

### Prerequisites:
1. **Jenkins** should be installed and running.
2. **Docker** should be installed and configured on the Jenkins server (so Jenkins can build Docker images).
3. The **GitHub repo** URL: `https://github.com/stv707/app-x.git`.
4. Jenkins should have Docker and Git plugins installed.

---

### Part 1: Jenkins Job Setup

#### Step 1: Create a New Jenkins Job
1. Log in to Jenkins.
2. Click on **New Item**.
3. Enter the job name (e.g., `Build App-X Docker`), and select **Freestyle Project**.
4. Click **OK** to proceed.

#### Step 2: Set Up Git Repository
1. In the **Source Code Management** section, select **Git**.
2. In the **Repository URL**, enter:
   ```bash
   https://github.com/stv707/app-x.git
   ```
3. Set your **GitHub credentials** if required.

#### Step 3: Define Build Environment Variable
1. Scroll down to **Build Environment** and check **Use secret text(s) or file(s)** (if necessary) to add the `BUILD_ENV` variable, or configure it as a parameter in the Jenkins job. 
   - To add as a parameter:
     - Check **This project is parameterized**.
     - Select **String Parameter**, and name it `BUILD_ENV`.
     - Set the default value to, for example, `dev`.

#### Step 4: Add Build Steps to Build the Docker Image
1. Scroll to the **Build** section, and click on **Add build step** > **Execute shell**.
2. In the **Command** section, add the following script:

   ```bash
   # Navigate to the workspace where the repo is cloned
   cd $WORKSPACE

   # Build the Docker image from the Dockerfile and tag it using BUILD_ENV
   docker build -t stv707/appx:$BUILD_ENV .

   # Stop any running container with the same name
   docker stop appx || true && docker rm appx || true

   # Run the newly built Docker container, exposing port 3000
   docker run -d --name appx -p 3000:3000 stv707/appx:$BUILD_ENV
   ```

This script will:
- Navigate to the workspace where Jenkins has cloned your GitHub repo.
- Build the Docker image from the Dockerfile, tag it with `stv707/appx:(BUILD_ENV)`.
- Stop any running container named `appx`, remove it, and run the new container while exposing port 3000.

#### Step 5: Save and Build the Jenkins Job
1. Click **Save** to store your job configurations.
2. To test the setup, click **Build Now** to trigger a build.

---

### Part 2: Jenkins Job Execution

- Once the build is triggered, Jenkins will:
  1. Clone the `app-x` repo from GitHub.
  2. Use the `Dockerfile` to build the Docker image.
  3. Tag the image as `stv707/appx:(BUILD_ENV)`.
  4. Run the container, exposing port 3000.

### Verifying the Container
1. You can verify that the container is running by accessing the Jenkins server on port 3000. 
   - Open a browser and go to `http://<Jenkins_server_IP>:3000`.

---

Let me know if you need additional configuration or further assistance with Jenkins!
