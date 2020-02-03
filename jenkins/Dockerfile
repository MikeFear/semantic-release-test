FROM maven:3-jdk-11-slim

RUN apt update && apt install -y git tzdata curl
RUN cp /usr/share/zoneinfo/Europe/Berlin /etc/localtime && \
    echo "Europe/Berlin" > /etc/timezone && \
    apt remove -y tzdata
RUN curl -sL https://deb.nodesource.com/setup_12.x | bash -
RUN apt update && apt install -y nodejs
RUN useradd -m jenkins
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x entrypoint.sh
USER jenkins
RUN mkdir -p /home/jenkins/.m2
RUN mkdir -p /home/jenkins/.ssh
RUN ssh-keyscan -p 7999 -H git.zueri.io >> /home/jenkins/.ssh/known_hosts
VOLUME /home/jenkins

# Override unnecessary entrypoint script from base image
ENTRYPOINT ["/entrypoint.sh"]
