FROM debian:bookworm-slim

ENV BITCOIN_VERSION 27.0

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
           export BITCOIN_URL="https://bitcoin.org/bin/bitcoin-core-27.0/bitcoin-27.0-aarch64-linux-gnu.tar.gz" ;\
           export BITCOIN_SHA256="cb35e250ae9d0328aa90e7aad0b877ed692597420a1092e8ab1a5dd756209722" ;\
           env ;\
           ;; \
         amd64) \
           echo "Target: $TARGETARCH"; \
           export BITCOIN_URL="https://bitcoin.org/bin/bitcoin-core-27.0/bitcoin-27.0-x86_64-linux-gnu.tar.gz" ;\
           export BITCOIN_SHA256="2a6974c5486f528793c79d42694b5987401e4a43c97f62b1383abf35bcee44a8" ;\
           ;; \
         arm) \
           echo "Target: $TARGETARCH"; \
           export BITCOIN_URL="https://bitcoin.org/bin/bitcoin-core-27.0/bitcoin-27.0-arm-linux-gnueabihf.tar.gz" ;\
           export BITCOIN_SHA256="9d4c28e7620d03bf346ebea388f222e4d6d2b996d7eb32fab72707b8320d5249" ;\
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
