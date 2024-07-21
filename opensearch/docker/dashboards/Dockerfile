# Copyright OpenSearch Contributors
# SPDX-License-Identifier: Apache-2.0


# This dockerfile generates an AmazonLinux-based image containing an OpenSearch-Dashboards installation.
# It assumes that the working directory contains four files: an OpenSearch-Dashboards tarball (opensearch-dashboards.tgz), opensearch_dashboards.yml, opensearch-dashboards-docker-entrypoint.sh, and example certs.
# Build arguments:
#   VERSION: Required. Used to label the image.
#   UID: Optional. Specify the opensearch-dashboards userid. Defaults to 1000.
#   GID: Optional. Specify the opensearch-dashboards groupid. Defaults to 1000.
#   OPENSEARCH_DASHBOARDS_HOME: Optional. Specify the opensearch-dashboards root directory. Defaults to /usr/share/opensearch-dashboards.

########################### Stage 0 ########################
FROM ubuntu:focal AS linux_stage_0

ARG UID=1000
ARG GID=1000
ARG OPENSEARCH_DASHBOARDS_HOME=/usr/share/opensearch-dashboards
ARG TEMP_DIR=/tmp/opensearch-dashboards
ARG OSD_VERSION=2.14.0
ARG ARCH=x64

RUN apt update -y && apt install curl -y

# Create an opensearch-dashboards user, group, and directory
RUN groupadd --gid $GID opensearch-dashboards && \
    adduser --uid $UID --gid $GID --home $OPENSEARCH_DASHBOARDS_HOME opensearch-dashboards && \
    mkdir $TEMP_DIR

RUN mkdir -p /usr/share/opensearch-dashboards

WORKDIR /usr/share/opensearch-dashboards

RUN curl -LO https://artifacts.opensearch.org/releases/bundle/opensearch-dashboards/$OSD_VERSION/opensearch-dashboards-$OSD_VERSION-linux-$ARCH.tar.gz 
RUN tar xvf opensearch-dashboards-$OSD_VERSION-linux-$ARCH.tar.gz -C /usr/share/opensearch-dashboards --strip-components=1
RUN rm -f opensearch-dashboards-$OSD_VERSION-linux-$ARCH.tar.gz

COPY config/* $OPENSEARCH_DASHBOARDS_HOME/config/
COPY bin/* $OPENSEARCH_DASHBOARDS_HOME/

########################### Stage 1 ########################
# Copy working directory to the actual release docker images
FROM ubuntu:focal

ARG UID=1000
ARG GID=1000
ARG OPENSEARCH_DASHBOARDS_HOME=/usr/share/opensearch-dashboards
ARG OSD_VERSION=2.5.0

# Update packages
# Install the tools we need: tar and gzip to unpack the OpenSearch tarball, and shadow-utils to give us `groupadd` and `useradd`.
# Install which to allow running of securityadmin.sh
#RUN yum update -y && yum install -y tar gzip shadow-utils which \
#    libnss3.so xorg-x11-fonts-100dpi xorg-x11-fonts-75dpi xorg-x11-utils \
#    xorg-x11-fonts-cyrillic xorg-x11-fonts-Type1 xorg-x11-fonts-misc fontconfig \
#    freetype && yum clean all

# Create an opensearch-dashboards user, group
RUN groupadd --gid $GID opensearch-dashboards && \
    adduser --uid $UID --gid $GID --home $OPENSEARCH_DASHBOARDS_HOME opensearch-dashboards

COPY --from=linux_stage_0 --chown=$UID:$GID $OPENSEARCH_DASHBOARDS_HOME $OPENSEARCH_DASHBOARDS_HOME

# Setup OpenSearch-dashboards
WORKDIR $OPENSEARCH_DASHBOARDS_HOME

# Set PATH
ENV PATH=$PATH:$OPENSEARCH_DASHBOARDS_HOME/bin

# Change user
USER $UID

# Expose port
EXPOSE 5601

# Label
LABEL org.label-schema.schema-version="1.0" \
  org.label-schema.name="opensearch-dashboards" \
  org.label-schema.version="$OSD_VERSION" \
  org.label-schema.url="https://opensearch.org" \
  org.label-schema.vcs-url="https://github.com/opensearch-project/OpenSearch-Dashboards" \
  org.label-schema.license="Apache-2.0" \
  org.label-schema.vendor="Amazon"

# CMD to run
ENTRYPOINT ["./opensearch-dashboards-docker-entrypoint.sh"]
CMD ["opensearch-dashboards"]

