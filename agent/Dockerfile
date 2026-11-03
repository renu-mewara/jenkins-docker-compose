FROM jenkins/inbound-agent:jdk25
ARG PYTHON_VERSION=3.12.3
ENV PYTHON_VERSION=${PYTHON_VERSION}
ENV PYTHON_MAJOR=3.12

USER root
ENV DEBIAN_FRONTEND=noninteractive

COPY manifest.txt /manifest.txt
RUN apt-get update -y && \
    . /manifest.txt && \
    apt-get install -y ${PACKAGES}

RUN git clone https://github.com/pyenv/pyenv.git /opt/pyenv && \
    export PYENV_ROOT="/opt/pyenv" && \
    export PATH="$PYENV_ROOT/bin:$PATH" && \
    /opt/pyenv/bin/pyenv install ${PYTHON_VERSION} && \
    /opt/pyenv/bin/pyenv global ${PYTHON_VERSION} && \
    mkdir -p /opt/python/${PYTHON_VERSION}/bin && \
    ln -s /opt/pyenv/versions/${PYTHON_VERSION}/bin/python${PYTHON_MAJOR} /opt/python/${PYTHON_VERSION}/bin/python${PYTHON_MAJOR} && \
    ln -s /opt/pyenv/versions/${PYTHON_VERSION}/bin/pip${PYTHON_MAJOR} /opt/python/${PYTHON_VERSION}/bin/pip${PYTHON_MAJOR}

RUN ln -s /opt/python/${PYTHON_VERSION}/bin/python${PYTHON_MAJOR} /usr/bin/python && \
    ln -s /opt/python/${PYTHON_VERSION}/bin/pip${PYTHON_MAJOR} /usr/bin/pip

RUN apt-get install -y sshpass krb5-user
RUN apt-get install -y poppler-utils

RUN wget -qO- https://www.postgresql.org/media/keys/ACCC4CF8.asc | gpg --dearmor -o /usr/share/keyrings/pgdg.gpg && \
    echo "deb [signed-by=/usr/share/keyrings/pgdg.gpg] http://apt.postgresql.org/pub/repos/apt/ $(. /etc/os-release && echo $VERSION_CODENAME)-pgdg main" > /etc/apt/sources.list.d/pgdg.list && \
    apt-get update -qq && \
    apt-get install -y postgresql-client-14

RUN wget -q https://releases.hashicorp.com/terraform/1.2.8/terraform_1.2.8_linux_amd64.zip && \
    unzip terraform_1.2.8_linux_amd64.zip && \
    mv terraform /usr/local/bin/ && \
    rm terraform_1.2.8_linux_amd64.zip

RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" && \
    unzip -q awscliv2.zip && \
    ./aws/install && \
    rm -rf aws awscliv2.zip

USER jenkins

ENV NVM_DIR=/home/jenkins/.nvm
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash && \
    . $NVM_DIR/nvm.sh && \
    nvm install 20 && \
    nvm use 20 && \
    nvm alias default 20 && \
    mkdir -p /home/jenkins/bin && \
    ln -sf $NVM_DIR/versions/node/v20.*/bin/node /home/jenkins/bin/node && \
    ln -sf $NVM_DIR/versions/node/v20.*/bin/npm /home/jenkins/bin/npm && \
    ln -sf $NVM_DIR/versions/node/v20.*/bin/npx /home/jenkins/bin/npx
ENV PATH="/home/jenkins/bin:$PATH"

RUN python -m venv /home/jenkins/venv
ENV VIRTUAL_ENV="/home/jenkins/venv"
ENV PATH="$VIRTUAL_ENV/bin:$PATH"

RUN echo "source /home/jenkins/venv/bin/activate" >> /home/jenkins/.bashrc
RUN echo "source /home/jenkins/venv/bin/activate" >> /home/jenkins/.profile

RUN pip install --upgrade pip "setuptools<81" wheel "Cython<3" && \
    pip install --no-build-isolation PyYAML==5.3.1

COPY --chown=jenkins:jenkins manifest.txt /tmp/manifest.txt
RUN . /tmp/manifest.txt && \
    pip install --no-build-isolation --upgrade $PIP_PACKAGES

ENV PATH="$PATH:/home/jenkins/.local/bin"

RUN ansible-galaxy collection install community.general && \
    ansible-galaxy collection install junipernetworks.junos

ENTRYPOINT ["/usr/local/bin/jenkins-agent"]
