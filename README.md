# Jenkins with Docker Compose (exp-tools agent, Java 25)

Controller: jenkins/jenkins:lts-jdk25
Agent: exp-tools image built from agent/Dockerfile (jenkins/inbound-agent:jdk25)
Tools: Python 3.12.3, Node 20, Ansible, Terraform, AWS CLI, psql 14

## Run
docker compose up -d --build

## Agent
1. Create node docker-agent (Permanent Agent, label docker-agent, root dir /home/jenkins/agent)
2. echo JENKINS_SECRET=SECRET > .env
3. docker compose --profile agent up -d --build (first build takes 10-15 min)
