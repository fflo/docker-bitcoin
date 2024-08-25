FROM debian:bookworm-slim

ENV BITCOIN_VERSION 22.0

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
           export BITCOIN_URL="https://bitcoin.org/bin/bitcoin-core-22.0/bitcoin-22.0-aarch64-linux-gnu.tar.gz" ;\
           export BITCOIN_SHA256="ac718fed08570a81b3587587872ad85a25173afa5f9fbbd0c03ba4d1714cfa3e" ;\
           env ;\
           ;; \
         amd64) \
           echo "Target: $TARGETARCH"; \
           export BITCOIN_URL="https://bitcoin.org/bin/bitcoin-core-22.0/bitcoin-22.0-x86_64-linux-gnu.tar.gz" ;\
           export BITCOIN_SHA256="59ebd25dd82a51638b7a6bb914586201e67db67b919b2a1ff08925a7936d1b16" ;\
           ;; \
         arm) \
           echo "Target: $TARGETARCH"; \
           export BITCOIN_URL="https://bitcoin.org/bin/bitcoin-core-22.0/bitcoin-22.0-arm-linux-gnueabihf.tar.gz" ;\
           export BITCOIN_SHA256="b8713c6c5f03f5258b54e9f436e2ed6d85449aa24c2c9972f91963d413e86311" ;\
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
