FROM registry.opensuse.org/opensuse/tumbleweed:latest

ENV LANG="en_US.UTF-8"
RUN zypper --non-interactive ref && zypper --non-interactive in tgt
RUN dd if=/dev/zero of=/opt/iscsi-disk seek=1M bs=20480 count=1

# Add config for the target
ADD iscsi-target.conf /etc/tgt/conf.d

# Expose default port for the service
EXPOSE 3260

# Start daemon and reload config. Keep container running as long as service is alive
CMD /usr/sbin/tgtd && tgt-admin --update ALL --force; while pgrep tgtd; do sleep 100; done
