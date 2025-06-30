FROM jenkins/inbound-agent:latest

USER root

# Install Docker and clean up
RUN apt-get update && apt-get install -y docker.io && rm -rf /var/lib/apt/lists/*

# Add jenkins user to docker group ✅
RUN usermod -aG docker jenkins

USER jenkins
