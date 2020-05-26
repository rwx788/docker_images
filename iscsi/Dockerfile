FROM registry.opensuse.org/opensuse/tumbleweed:latest

ENV LANG="en_US.UTF-8"
RUN zypper ref; zypper --non-interactive in --no-recommends tgt

# Add config for the target
ADD iscsi-target.conf /etc/tgt/conf.d

# Expose default port for the service
EXPOSE 3260
# Add volume mount point
VOLUME ["/opt/iscsi-disk"]

# Start daemon and reload config. Keep container running as long as service is alive
CMD /usr/sbin/tgtd && tgt-admin --update ALL --force; while pgrep tgtd &> /dev/null; do sleep 100; done
