FROM registry.fedoraproject.org/fedora-minimal:33
# Metadata
LABEL maintainer="Giacomo Longo <gabibbo97@gmail.com>"
LABEL fedoraproject.org/version=33
LABEL directory.fedoraproject.org/version=1.4
EXPOSE 3389 3636
# Install package
RUN microdnf install -y 389-ds-base && microdnf clean all
# Create state directories
RUN \
  # Needed for successful startup
  mkdir -p /var/run/dirsrv && ln -sf /data/run /etc/dirsrv/run && \
  # Needed to persist data
  mkdir -p /data/config && ln -sf /data/config /etc/dirsrv/slapd-localhost && \
  mkdir -p /data/ssca && ln -sf /data/ssca /etc/dirsrv/ssca
VOLUME /data
# Set healthcheck
HEALTHCHECK --interval=60s --start-period=30s \
  CMD /usr/libexec/dirsrv/dscontainer -H
# Set entrypoint
ENTRYPOINT [ "/usr/libexec/dirsrv/dscontainer", "-r" ]
