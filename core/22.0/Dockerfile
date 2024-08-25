FROM debian:bullseye-slim

RUN groupadd -r bitcoin && useradd -r -m -g bitcoin bitcoin

RUN set -ex \
	&& apt-get update \
	&& apt-get install -qq --no-install-recommends ca-certificates dirmngr gosu gpg gpg-agent wget \
	&& rm -rf /var/lib/apt/lists/*

ENV BITCOIN_VERSION 22.0
ENV BITCOIN_URL https://bitcoin.org/bin/bitcoin-core-22.0/bitcoin-22.0-x86_64-linux-gnu.tar.gz
ENV BITCOIN_SHA256 59ebd25dd82a51638b7a6bb914586201e67db67b919b2a1ff08925a7936d1b16 
ENV BITCOIN_ASC_URL https://bitcoin.org/bin/bitcoin-core-22.0/SHA256SUMS.asc
ENV BITCOIN_PGP_KEY 74E2DEF5D77260B98BC19438099BAD163C70FBFA,9D3CC86A72F8494342EA5FD10A41BDC3F4FAFF1C,9D3CC86A72F8494342EA5FD10A41BDC3F4FAFF1C 

# install bitcoin binaries
RUN set -ex \
	&& cd /tmp \
	&& wget -qO bitcoin.tar.gz "$BITCOIN_URL" \
	&& echo "$BITCOIN_SHA256 bitcoin.tar.gz" | sha256sum -c - \
	&& gpg --keyserver keyserver.ubuntu.com --recv-keys "$BITCOIN_PGP_KEY" \
	&& wget -qO bitcoin.asc "$BITCOIN_ASC_URL" \
	&& gpg --verify bitcoin.asc \
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
