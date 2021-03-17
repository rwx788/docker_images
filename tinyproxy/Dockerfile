FROM registry.opensuse.org/opensuse/tumbleweed:latest

ENV LANG="en_US.UTF-8"
RUN zypper --non-interactive in --no-recommends tinyproxy

COPY tinyproxy.conf /etc/tinyproxy/tinyproxy.conf

# Expose default port for the service
EXPOSE 8888

# Start daemon. Keep container running as long as service is alive
CMD /usr/sbin/tinyproxy; while pgrep tinyproxy &> /dev/null; do sleep 100; done
