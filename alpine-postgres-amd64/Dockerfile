FROM alpine:latest
LABEL postgres-version="11.1" alpine-version="3.9.0" date="11-Feb-2018"

ENV LANG en_US.utf8
ENV PGDATA /var/lib/postgresql/data

ENV GOSU_VERSION="1.11" \
	GOSU_DOWNLOAD_URL="https://github.com/tianon/gosu/releases/download/1.11/gosu-amd64" \
	GOSU_DOWNLOAD_SIG="https://github.com/tianon/gosu/releases/download/1.11/gosu-amd64.asc" \
	GOSU_DOWNLOAD_KEY="0x036A9C25BF357DD4"


RUN buildDeps='curl gnupg' HOME='/root' \
	&& set -x \
	&& apk add --update $buildDeps \
	&& gpg-agent --daemon \
	&& gpg --keyserver pgp.mit.edu --recv-keys $GOSU_DOWNLOAD_KEY \
	&& echo "trusted-key $GOSU_DOWNLOAD_KEY" >> /root/.gnupg/gpg.conf \
	&& curl -sSL "$GOSU_DOWNLOAD_URL" > gosu-amd64 \
	&& curl -sSL "$GOSU_DOWNLOAD_SIG" > gosu-amd64.asc \
	&& gpg --verify gosu-amd64.asc \
	&& rm -f gosu-amd64.asc \
	&& mv gosu-amd64 /usr/bin/gosu \
	&& chmod +x /usr/bin/gosu \
	&& apk del --purge $buildDeps \
	&& rm -rf /root/.gnupg \
	&& rm -rf /var/cache/apk/* \
    ;


RUN apk update && \
    apk add su-exec tzdata libpq postgresql-client postgresql postgresql-contrib && \
    mkdir /docker-entrypoint-initdb.d && \
    rm -rf /var/cache/apk/*


VOLUME /var/lib/postgresql/data

COPY files/docker-entrypoint.sh /

RUN chmod -R 755 /docker-entrypoint.sh && \
    mkdir -p /run/postgresql && \
    chown postgres: /run/postgresql

ENTRYPOINT ["/docker-entrypoint.sh"]

EXPOSE 5432
CMD ["postgres"]
