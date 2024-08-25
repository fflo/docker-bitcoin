FROM debian:bookworm-slim

ENV BITCOIN_VERSION 25.0

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
           export BITCOIN_URL="https://bitcoin.org/bin/bitcoin-core-25.0/bitcoin-25.0-aarch64-linux-gnu.tar.gz" ;\
           export BITCOIN_SHA256="3a7bdd959a0b426624f63f394f25e5b7769a5a2f96f8126dcc2ea53f3fa5212b" ;\
           env ;\
           ;; \
         amd64) \
           echo "Target: $TARGETARCH"; \
           export BITCOIN_URL="https://bitcoin.org/bin/bitcoin-core-25.0/bitcoin-25.0-x86_64-linux-gnu.tar.gz" ;\
           export BITCOIN_SHA256="33930d432593e49d58a9bff4c30078823e9af5d98594d2935862788ce8a20aec" ;\
           ;; \
         arm) \
           echo "Target: $TARGETARCH"; \
           export BITCOIN_URL="https://bitcoin.org/bin/bitcoin-core-25.0/bitcoin-25.0-arm-linux-gnueabihf.tar.gz" ;\
           export BITCOIN_SHA256="e537c8630b05e63242d979c3004f851fd73c2a10b5b4fdbb161788427c7b3c0f" ;\
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
