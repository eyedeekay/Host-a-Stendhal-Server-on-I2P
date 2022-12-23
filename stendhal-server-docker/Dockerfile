FROM debian:11.4-slim AS apt
# avoid "debconf: unable to initialize frontend: Dialog"
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update \
    && apt-get install --yes apt-utils

FROM apt AS jre
RUN apt-get install --yes openjdk-11-jre-headless

FROM apt AS build
ARG stendhal_version=1.41.2
ARG zip_file=stendhal-server-${stendhal_version}.zip
RUN apt-get update \
    && apt-get install --yes \
      patch \
      unzip \
      wget \
    && wget --output-document=${zip_file} \
      https://sourceforge.net/projects/arianne/files/stendhal/${stendhal_version}/${zip_file}/download \
    && mkdir /stendhal \
    && unzip ${zip_file} -d /stendhal \
    && chmod +x /stendhal/runserver.sh
WORKDIR /stendhal
COPY \
    server.ini \
    runserver_exec.patch \
    ./
RUN patch --strip=0 runserver.sh runserver_exec.patch \
    && rm -f runserver_exec.patch
RUN apt-get remove --yes \
      patch \
      unzip \
      wget

FROM jre
ARG port=32160
WORKDIR /stendhal
COPY --from=build /stendhal ./
EXPOSE ${port}
ENTRYPOINT ["./runserver.sh"]
