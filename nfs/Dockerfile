FROM registry.opensuse.org/opensuse/tumbleweed:latest

ENV LANG="en_US.UTF-8"
RUN zypper --non-interactive in --no-recommends nfs-kernel-server util-linux

# Add config for the exports
ADD exports /etc/exports

# Expose default port for the service
EXPOSE 2049
# Add volume mount point
VOLUME ["/export/data"]

# Start daemon and reload config. Keep container running as long as service is alive
CMD exportfs -r & /usr/sbin/rpc.mountd & /usr/sbin/rpc.nfsd; while pgrep mountd &> /dev/null; do sleep 100; done
