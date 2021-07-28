#
# Dockerfile for samba (without netbios)
#

FROM alpine

RUN apk add --update \
    bash \
    samba-common-tools \
    samba-client \
    samba-server \
    && rm -rf /var/cache/apk/*

# Create smb group, smduser, set password to "smbuser" and add smbuser to smb group
RUN addgroup -S smb && \
    adduser -S -D -H -h /tmp -s /sbin/nologin -G smb -g 'Samba User' smbuser && \
    echo -ne "smbuser\nsmbuser" | smbpasswd -a -s smbuser

COPY smb.conf /etc/samba/smb.conf
EXPOSE 445/tcp

# Start daemon and reload config. Keep container running as long as service is alive
CMD /usr/sbin/smbd; while pgrep smbd &> /dev/null; do sleep 100; done
