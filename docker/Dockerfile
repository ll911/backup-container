# This image provides a postgres installation from which to run backups 
FROM image-registry.openshift-image-registry.svc:5000/7f7ed0-tools/patroni:pg12-postgis3

# Change timezone to PST for convenience
ENV TZ=PST8PDT

# Set the workdir to be root
WORKDIR /

# Load the backup scripts into the container (must be executable).
COPY backup.* /

COPY webhook-template.json /

# ========================================================================================================
# Install go-crond (from https://github.com/BCDevOps/go-crond)
#  - Adds some additional logging enhancements on top of the upstream project; 
#    https://github.com/webdevops/go-crond
#
# CRON Jobs in OpenShift:
#  - https://blog.danman.eu/cron-jobs-in-openshift/
# --------------------------------------------------------------------------------------------------------
ARG GOCROND_VERSION=20.7.0
    
ADD https://github.com/webdevops/go-crond/releases/download/$GOCROND_VERSION/go-crond-64-linux /usr/bin/go-crond

USER root
SHELL ["/bin/bash", "-c"]
RUN apt-get -y update && \
  apt-get -y install --no-install-recommends bsdmainutils && \ 
  chmod 755 /usr/bin/go-crond && \
  echo $TZ > /etc/timezone

# Important - Reset to the base image's user account.
USER postgres

# Set the default CMD.
#CMD bash /backup.sh
ENTRYPOINT ["/bin/bash", "-c", "/backup.sh"]
