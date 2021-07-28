#
# Dockerfile for samba (without netbios)
#

FROM alpine

RUN apk --no-cache --no-progress upgrade && \
    apk add --no-cache --no-progress bash samba-common-tools samba-client samba-server

# Create smb group, smduser, set password to "smbuser" and add smbuser to smb group
RUN addgroup -S smb && \
    adduser -S -D -H -h /tmp -s /sbin/nologin -G smb -g 'Samba User' smbuser && \
    echo -ne "smbuser\nsmbuser" | smbpasswd -a -s smbuser

COPY smb.conf /etc/samba/smb.conf
EXPOSE 445/tcp

# Start smb daemon. Keep container running as long as service is alive
CMD /usr/sbin/smbd; while pgrep smbd &> /dev/null; do sleep 100; done
