FROM debian:bookworm-slim

ENV BITCOIN_VERSION 27.1

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
           export BITCOIN_URL="https://bitcoincore.org/bin/bitcoin-core-27.1/bitcoin-27.1-aarch64-linux-gnu.tar.gz" ;\
           export BITCOIN_SHA256="bb878df4f8ff8fb8acfb94207c50f959c462c39e652f507c2a2db20acc6a1eee" ;\
           env ;\
           ;; \
         amd64) \
           echo "Target: $TARGETARCH"; \
           export BITCOIN_URL="https://bitcoincore.org/bin/bitcoin-core-27.1/bitcoin-27.1-x86_64-linux-gnu.tar.gz" ;\
           export BITCOIN_SHA256="c9840607d230d65f6938b81deaec0b98fe9cb14c3a41a5b13b2c05d044a48422" ;\
           ;; \
         arm) \
           echo "Target: $TARGETARCH"; \
           export BITCOIN_URL="https://bitcoincore.org/bin/bitcoin-core-27.1/bitcoin-27.1-arm-linux-gnueabihf.tar.gz" ;\
           export BITCOIN_SHA256="83bf6da65ebac189c7e14f1e68a758b32b8f83558b36b53a8cc8037c674ab045" ;\
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
