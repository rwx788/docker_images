FROM registry.opensuse.org/opensuse/tumbleweed:latest

ENV LANG="en_US.UTF-8"
RUN zypper --non-interactive in --no-recommends ref tgt

# Add config for the target
ADD iscsi-target.conf /etc/tgt/conf.d

# Expose default port for the service
EXPOSE 3260
# Add volume mount point
VOLUME ["/opt/iscsi-disk"]

# Start daemon and reload config. Keep container running as long as service is alive
CMD service nfs-server start; while pgrep nfsd &> /dev/null; do sleep 100; done
