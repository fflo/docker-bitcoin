FROM debian:bookworm-slim

ENV BITCOIN_VERSION 25.2

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
           export BITCOIN_URL="https://bitcoincore.org/bin/bitcoin-core-25.2/bitcoin-25.2-aarch64-linux-gnu.tar.gz" ;\
           export BITCOIN_SHA256="7928e046d83a9a4f82edee843a793bd0627294ac6309664bdfde1743afc1cfdb" ;\
           env ;\
           ;; \
         amd64) \
           echo "Target: $TARGETARCH"; \
           export BITCOIN_URL="https://bitcoincore.org/bin/bitcoin-core-25.2/bitcoin-25.2-x86_64-linux-gnu.tar.gz" ;\
           export BITCOIN_SHA256="8d8c387e597e0edfc256f0bbace1dac3ad1ebf4a3c06da3e2975fda333817dea" ;\
           ;; \
         arm) \
           echo "Target: $TARGETARCH"; \
           export BITCOIN_URL="https://bitcoincore.org/bin/bitcoin-core-25.2/bitcoin-25.2-arm-linux-gnueabihf.tar.gz" ;\
           export BITCOIN_SHA256="14a8b860f6f5635a02e81db5ad082224ae679aa0432bf97d75ce58b7ede242bc" ;\
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
