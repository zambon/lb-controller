FROM zambon/ubuntu-multi:16.04

RUN apt-get -q update && apt-get -qy install \
      jq \
      curl \
      tcpdump \
      vim-tiny \
      openssl \
      libssl-dev \
      rsyslog \
      haproxy \
      software-properties-common \
    && rm -rf /var/lib/apt/lists/*

RUN mkdir -p /etc/haproxy/

COPY lb-controller /usr/bin/
COPY scripts/* /usr/bin/
COPY config/* /etc/haproxy/

RUN mkdir /var/log/haproxy
RUN touch /var/log/haproxy/traffic /var/log/haproxy/events /var/log/haproxy/errors
RUN cat /etc/haproxy/logrotate.cfg >> /etc/logrotate.d/haproxy

ENV TINI_VERSION v0.16.1
ENV TINI_URL_default=https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini-${ARCH} \
    TINI_URL_amd64=TINI_URL_default \
    TINI_URL_arm=TINI_URL_default \
    TINI_URL_ppc64le=https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini-ppc64el \
    TINI_URL=TINI_URL_${ARCH}
RUN curl -fsSLo /tini ${!TINI_URL}
RUN chmod +x /tini

ENV SSL_SCRIPT_COMMIT 98660ada3d800f653fc1f105771b5173f9d1a019
RUN curl -fsSLo /usr/bin/update-rancher-ssl \
      https://raw.githubusercontent.com/rancher/rancher/${SSL_SCRIPT_COMMIT}/server/bin/update-rancher-ssl && \
    chmod +x /usr/bin/update-rancher-ssl

COPY lb-controller.sh /usr/bin/

# So we do not write to the COW filesystem
VOLUME /var/log/haproxy

ENTRYPOINT ["/tini", "--"]

CMD ["lb-controller.sh", "--controller", "rancher", "--provider",  "haproxy"]
