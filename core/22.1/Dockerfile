FROM debian:bookworm-slim

ENV BITCOIN_VERSION 22.1

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
           export BITCOIN_URL="https://bitcoincore.org/bin/bitcoin-core-22.1/bitcoin-22.1-aarch64-linux-gnu.tar.gz" ;\
           export BITCOIN_SHA256="a0dcdd1eb422ba8d0f409ebe909a40f5349b405e31231312f15b12fd2e9b6153" ;\
           env ;\
           ;; \
         amd64) \
           echo "Target: $TARGETARCH"; \
           export BITCOIN_URL="https://bitcoincore.org/bin/bitcoin-core-22.1/bitcoin-22.1-x86_64-linux-gnu.tar.gz" ;\
           export BITCOIN_SHA256="107e6a8d1f545de1299cbf93dfd7d41c9d67956f1ac9c910d7eaed1e363cfa2c" ;\
           ;; \
         arm) \
           echo "Target: $TARGETARCH"; \
           export BITCOIN_URL="https://bitcoincore.org/bin/bitcoin-core-22.1/bitcoin-22.1-arm-linux-gnueabihf.tar.gz" ;\
           export BITCOIN_SHA256="7046aa71c3f8247205ebbb838c8f0217552a499aac6281cea7f4747f4e42c2e9" ;\
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
