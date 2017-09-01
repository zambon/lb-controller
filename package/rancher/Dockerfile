FROM zambon/agent-base:v0.3.0
MAINTAINER Rancher Labs, Inc.

RUN apt-get -q update && apt-get -qy install \
      ca-certificates \
      openssl \
      libssl-dev \
      unzip \
    && rm -rf /var/lib/apt/lists/* && \
    rm -f /bin/sh && ln -s /bin/bash /bin/sh

ENV SSL_SCRIPT_COMMIT 98660ada3d800f653fc1f105771b5173f9d1a019
RUN curl -fsSLo /usr/bin/update-rancher-ssl \
      https://raw.githubusercontent.com/rancher/rancher/${SSL_SCRIPT_COMMIT}/server/bin/update-rancher-ssl && \
    chmod +x /usr/bin/update-rancher-ssl

COPY entry.sh /usr/bin/
COPY lb-controller /usr/bin/

ENTRYPOINT ["/usr/bin/entry.sh"]
