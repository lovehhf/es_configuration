# Build using: docker build -f Dockerfile.gocd-agent -t gocd-agent .
FROM gocd/gocd-agent:16.2.1

LABEL version="0.02" \
      description="This custom go-agent docker file installs additional requirements for the edx pipeline"

RUN \
  echo oracle-java7-installer shared/accepted-oracle-license-v1-1 select true | debconf-set-selections && \
  add-apt-repository -y ppa:webupd8team/java && \
  apt-get update && \
  apt-get install -y oracle-java7-installer && \
  rm -rf /var/lib/apt/lists/* && \
  rm -rf /var/cache/oracle-jdk7-installer

# Define working directory.
WORKDIR /data

# Define commonly used JAVA_HOME variable
ENV JAVA_HOME /usr/lib/jvm/java-7-oracle

# Install Python and package mgmt tools.
RUN apt-get update && apt-get install -y -q \
    python \
    python-dev \
    python-distribute \
    python-pip \
    libmysqlclient-dev

# Install Docker - for Docker container building by a go-agent.
COPY docker/build/go-agent/files/docker_install.sh /tmp/docker/
RUN /bin/bash /tmp/docker/docker_install.sh

# Assign the go user root privlidges
RUN printf "\ngo      ALL=(ALL:ALL) NOPASSWD: /usr/bin/pip\n" >> /etc/sudoers

# Install AWS command-line interface - for AWS operations in a go-agent task.
RUN pip install awscli

ADD docker/build/go-agent/files/go-agent-start.sh /etc/service/go-agent/run
ADD docker/build/go-agent/files/go-agent-env-vars /etc/default/go-agent
RUN update-java-alternatives -s java-7-oracle
