FROM alpine:3.11.0 as builder

MAINTAINER david@typokign.com

RUN apk add \
    autoconf \
    automake \
    gettext-dev \
    gcc \
    libc-dev \
    libtool \
    make \
    pkgconf \
    rpcgen \
    tini \
    wget

COPY dovecot /dovecot

WORKDIR /dovecot

RUN ./autogen.sh
RUN PANDOC=false ./configure --enable-maintainer-mode && \
    make && \
    make install

FROM alpine:3.11.0

COPY --from=builder /sbin/tini /sbin/tini
COPY --from=builder /usr/local/lib/dovecot /usr/local/lib/dovecot
COPY --from=builder /usr/local/libexec/dovecot /usr/local/libexec/dovecot
COPY --from=builder /usr/local/bin/doveadm /usr/local/bin/doveadm
COPY --from=builder /usr/local/bin/doveconf /usr/local/bin/doveconf
COPY --from=builder /usr/local/bin/dsync /usr/local/bin/dsync
COPY --from=builder /usr/local/sbin/dovecot /usr/local/sbin/dovecot

COPY files/dovecot.conf /etc/dovecot/dovecot.conf
COPY files/include.conf /usr/local/etc/dovecot/dovecot.conf

RUN addgroup -g 1000 -S dovecot && \
    adduser -u 1000 -S -g dovecot dovecot && \
    addgroup -g 1001 -S dovenull && \
    adduser -u 1001 -S -g dovenull dovenull

ENTRYPOINT ["/sbin/tini"]
CMD ["dovecot", "-F"]
