FROM java:7-jre

# Download version 1.5.2 of logstash
RUN cd /tmp && \
    wget https://download.elasticsearch.org/logstash/logstash/logstash-1.5.2.tar.gz && \
    tar -xzvf ./logstash-1.5.2.tar.gz && \
    mv ./logstash-1.5.2 /opt/logstash && \
    rm ./logstash-1.5.2.tar.gz

# Install contrib plugins
RUN /opt/logstash/bin/plugin install contrib

# Copy build files to container root
RUN mkdir /usr/share/logstash
ADD . /usr/share/logstash

# Set default data directory
ENV DATA_DIR /usr/share/logstash

# Define mountable directory
VOLUME ${DATA_DIR}

# Kibana
EXPOSE 9292

# Start logstash
ENTRYPOINT ["/app/bin/boot"]

# Valid commands: `agent`, `web`, `configtest`
# Default (empty command) runs the ELK stack
CMD []