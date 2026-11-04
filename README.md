# Jenkins with Docker Compose (exp-tools agent, Java 25)

- Controller: `jenkins/jenkins:lts-jdk25`
- Agent image: `ghcr.io/renu-mewara/exp-tools-inbound-agent:jdk25`
  (built from `agent/Dockerfile`, base `jenkins/inbound-agent:jdk25`)
- Tools in agent: Python 3.12.3, Node 20, Ansible, Terraform, AWS CLI, psql 14

## Run the controller
```bash
docker compose up -d --build
```

## Use the image with Jenkins Docker Cloud
1. Manage Jenkins > Plugins > install **Docker** plugin
2. Manage Jenkins > Clouds > New cloud (type Docker)
   - Docker Host URI: `unix:///var/run/docker.sock`
3. Add Docker Template
   - Labels: `exp-tools`
   - Docker Image: `ghcr.io/renu-mewara/exp-tools-inbound-agent:jdk25`
   - Network: `jenkins-docker-compose_default`
   - Connect method: Connect with JNLP, Jenkins URL `http://jenkins:8080`
   - Remote File System Root: `/home/jenkins/agent`
4. Run the `Jenkinsfile` in this repo (`agent { label 'exp-tools' }`)

## Permanent agent (optional)
```bash
echo 'JENKINS_SECRET=<secret>' > .env
docker compose --profile agent up -d
```

## Rebuild the image (only if agent/ changes)
```bash
docker build -t ghcr.io/renu-mewara/exp-tools-inbound-agent:jdk25 ./agent
```