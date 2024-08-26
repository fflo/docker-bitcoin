FROM debian:bookworm-slim

ENV BITCOIN_VERSION 23.2

# add multiple platform support
ARG TARGETARCH
RUN echo "Ok, I'm building $BITCOIN_VERSION for $TARGETARCH"

RUN groupadd -r bitcoin && useradd -r -m -g bitcoin bitcoin

# download dependencies
RUN set -ex \
    && apt-get update \
    && apt-get install -qq --no-install-recommends ca-certificates dirmngr gosu gpg gpg-agent wget \
    && rm -rf /var/lib/apt/lists/*

# download and install bitcoin binaries
RUN set -ex \
    && cd /tmp \
    && case ${TARGETARCH} in \
         arm64) \
           echo "Target: $TARGETARCH"; \
           export BITCOIN_URL="https://bitcoincore.org/bin/bitcoin-core-23.2/bitcoin-23.2-aarch64-linux-gnu.tar.gz" ;\
           export BITCOIN_SHA256="160ce6ce7185faa0db242953b5f93b108835d3790d3ea9ac689f21a85250be35" ;\
           env ;\
           ;; \
         amd64) \
           echo "Target: $TARGETARCH"; \
           export BITCOIN_URL="https://bitcoincore.org/bin/bitcoin-core-23.2/bitcoin-23.2-x86_64-linux-gnu.tar.gz" ;\
           export BITCOIN_SHA256="853b9b0a50800a5b355df7a39dbdd6d7a2339f765e2d31a36d14bd705e7dbce1" ;\
           ;; \
         arm) \
           echo "Target: $TARGETARCH"; \
           export BITCOIN_URL="https://bitcoincore.org/bin/bitcoin-core-23.2/bitcoin-23.2-arm-linux-gnueabihf.tar.gz" ;\
           export BITCOIN_SHA256="ded39ea01b607c5db5c765b35cc17ad9599dcb56d4051558e63d3b13ea6f8756" ;\
           ;; \
         *) \
           echo "unable to build for target: $TARGETARCH" ;\
           exit 1 ;\
           ;; \
       esac \
    && wget -qO bitcoin.tar.gz "$BITCOIN_URL" \
    && echo "$BITCOIN_SHA256 bitcoin.tar.gz" | sha256sum -c - \
    && tar -xzvf bitcoin.tar.gz -C /usr/local --strip-components=1 --exclude=*-qt \
    && rm -rf /tmp/*

# create data directory
ENV BITCOIN_DATA /data
RUN mkdir "$BITCOIN_DATA" \
    && chown -R bitcoin:bitcoin "$BITCOIN_DATA" \
    && ln -sfn "$BITCOIN_DATA" /home/bitcoin/.bitcoin \
    && chown -h bitcoin:bitcoin /home/bitcoin/.bitcoin
VOLUME /data

COPY docker-entrypoint.sh /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]

EXPOSE 8332 8333 18332 18333
CMD ["bitcoind"]
