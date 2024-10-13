FROM jenkins/jenkins

# Switch to root to install Docker CLI and make system changes
USER root

# Install dependencies for Docker
RUN apt-get update && apt-get install -y lsb-release \
    && curl -fsSLo /usr/share/keyrings/docker-archive-keyring.asc https://download.docker.com/linux/debian/gpg \
    && echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.asc] https://download.docker.com/linux/debian $(lsb_release -cs) stable" > /etc/apt/sources.list.d/docker.list \
    && apt-get update && apt-get install -y docker-ce-cli

# Create the Docker group (if not already created) and add Jenkins user to the group
RUN groupadd -f docker && usermod -aG docker jenkins

# Switch back to the Jenkins user to run Jenkins as non-root
USER jenkins

# Install Jenkins plugins
RUN jenkins-plugin-cli --plugins "blueocean docker-workflow"
