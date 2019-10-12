# Docker image for Apache + OpenAM HTTP agent
FROM httpd:2.2.29

WORKDIR /opt


ADD agent.zip /var/tmp/agent.zip
RUN apt-get install debian-archive-keyring
RUN apt-get update && apt-get install -y --no-install-recommends apt-utils curl wget vim
#RUN apt-get remove libssl-dev openssl -y
#RUN apt-get autoremove -y
#RUN wget http://archive.ubuntu.com/ubuntu/pool/main/o/openssl/openssl_1.1.1b-1ubuntu2_amd64.deb
#RUN dpkg -i ./openssl*.deb


RUN apt-get install --no-install-recommends  -y unzip curl && \
      unzip /var/tmp/agent.zip && rm /var/tmp/agent.zip && \
      echo "password" >/var/tmp/pw.txt  && chmod 0400 /var/tmp/pw.txt && \
      rm -rf /var/lib/apt/lists/*


# Format of agentadmin command:
# agentadmin --s "web-server configuration file, directory or site parameter" \
#                "OpenAM URL" "Agent URL" "realm" "agent user id" \
#                "path to the agent password file" [--changeOwner] [--acceptLicence] [--forceInstall]
#

# Because we are running this before OpenAM is up and running we need to use the forceinstall option
# We are using some defaults here (openam service name) that are not going to exist in most environments
# They will need to be overridden at runtime by updating the bootstrap files
# A possible option is to have Docker create a host entry for openam-svc - but that seems kludgy...
RUN /opt/web_agents/apache22_agent/bin/agentadmin --s /usr/local/apache2/conf/httpd.conf \
        "http://openam.example.com:8080/am" \
        "http://agent.example.com:80" \
        "/" \
        "WebAgent" \
        /var/tmp/pw.txt \
        --changeOwner --acceptLicence --forceInstall

# Cache size in bytes. The default is 1.5 GB. On Docker you need to run with --shm-size
# Set this down lower for testing purposes
ENV AM_MAX_SHARED_POOL_SIZE 1048576000

# todo: We should let above command create the conf file
#COPY agent.conf /opt/web_agents/apache24_agent/instances/agent_1/config/agent.conf
COPY run.sh /var/tmp/

# Inherit CMD from Parent httpd:2.4
# CMD ["httpd-foreground"]

CMD ["/var/tmp/run.sh"]
