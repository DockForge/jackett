# syntax=docker/dockerfile:1

FROM dublok/base-linux:alpine-v1

# set version label
ARG BUILD_DATE
ARG VERSION
ARG JACKETT_RELEASE
LABEL build_version="Version:- ${VERSION} Build-date:- ${BUILD_DATE}"
LABEL maintainer="Dublok"
ARG TARGETPLATFORM=""
ARG ARCH=""

RUN echo "Building for platform: ${TARGETPLATFORM}"
RUN echo "Architecture set to: ${ARCH}"

# environment settings
ENV XDG_DATA_HOME="/config" \
XDG_CONFIG_HOME="/config"

RUN \
  echo "**** install packages ****" && \
  apk --no-cache add \
    icu-data-full \
    icu-libs && \
  echo "**** install jackett ****" && \
  mkdir -p \
    /app/Jackett && \
  if [ -z ${JACKETT_RELEASE+x} ]; then \
    JACKETT_RELEASE=$(curl -sX GET "https://api.github.com/repos/Jackett/Jackett/releases/latest" \
    | jq -r .tag_name); \
  fi && \
  curl -o \
    /tmp/jacket.tar.gz -L \
    "https://github.com/Jackett/Jackett/releases/download/${JACKETT_RELEASE}/Jackett.Binaries.LinuxMuslAMDx64.tar.gz" && \
  tar xf \
    /tmp/jacket.tar.gz -C \
    /app/Jackett --strip-components=1 && \
  echo "**** fix for host id mapping error ****" && \
  chown -R root:root /app/Jackett && \
  echo "**** save docker image version ****" && \
  echo "${VERSION}" > /etc/docker-image && \
  printf "Version: ${VERSION}\nBuild-date: ${BUILD_DATE}" > /build_version && \
  echo "**** cleanup ****" && \
  rm -rf \
    /tmp/*

# add local files
COPY root/ /

# Ensure correct permissions for all scripts in s6-overlay directory
RUN find /etc/s6-overlay/ -type f -exec chmod 744 {} \; && \
    chmod 744 /docker-mods

# ports and volumes
VOLUME /config

EXPOSE 9117