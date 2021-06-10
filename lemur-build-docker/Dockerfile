#############  BUILDER  #############
FROM ubuntu:20.04 as builder

ARG DEBIAN_FRONTEND=noninteractive
ARG VERSION

ARG URLCONTEXT

COPY lemur/ /opt/lemur
WORKDIR /opt/lemur

RUN apt-get update && \
    apt-get -y --no-install-recommends upgrade && \
    apt-get install -y --no-install-recommends libpq-dev curl build-essential locales libffi-dev libsasl2-dev libldap2-dev \
        dh-autoreconf git python3-dev python3-pip python3-venv python3-wheel nodejs npm supervisor postgresql-client && \
    locale-gen en_US.UTF-8 && export LC_ALL=en_US.UTF-8 && \
    npm config set registry http://registry.npmjs.org/ && \
    npm install npm -g && \
    echo "Running with nodejs:" && node -v && \
    python3 -m venv /opt/venv && \
    echo "Running with python:" && /opt/venv/bin/python3 -c 'import platform; print(platform.python_version())' && \
    /opt/venv/bin/python3 -m pip install --no-cache-dir --upgrade pip setuptools wheel && \
    /opt/venv/bin/python3 -m pip install --no-cache-dir -e . && \
    npm install --unsafe-perm && \
    node_modules/.bin/gulp --cwd /opt/lemur build && \
    node_modules/.bin/gulp --cwd /opt/lemur package && \
    npm cache clean --force && \
    rm -rf node_modules && \
    python3 -c 'print(" \033[32m BUILDER DONE \033[0m ")'

RUN mkdir -p /opt/lemur /home/lemur/.lemur/ && \
    touch /home/lemur/.lemur/lemur.log && \
    chmod o+w /home/lemur/.lemur/lemur.log

#############  APP  #############
FROM gcr.io/distroless/base-debian10

# /bin/bash
COPY --from=builder /bin/bash /bin/bash
COPY --from=builder /lib/x86_64-linux-gnu/libtinfo.so.6 /lib/x86_64-linux-gnu/libtinfo.so.6

# python3
COPY --from=builder /usr/bin/python3 /usr/bin/python3
COPY --from=builder /lib/x86_64-linux-gnu/libexpat.so.1 /lib/x86_64-linux-gnu/libexpat.so.1
COPY --from=builder /lib/x86_64-linux-gnu/libz.so.1 /lib/x86_64-linux-gnu/libz.so.1
COPY --from=builder /lib/x86_64-linux-gnu/libm.so.6 /lib/x86_64-linux-gnu/libm.so.6
COPY --from=builder /usr/lib/python3.8 /usr/lib/python3.8
COPY --from=builder /usr/bin/pip /usr/bin/pip
COPY --from=builder /usr/bin/pip3 /usr/bin/pip3
COPY --from=builder /usr/lib/python3 /usr/lib/python3

# for python modules
COPY --from=builder /usr/lib/x86_64-linux-gnu/libldap_r-2.4.so.2 /usr/lib/x86_64-linux-gnu/libldap_r-2.4.so.2
COPY --from=builder /usr/lib/x86_64-linux-gnu/liblber-2.4.so.2 /usr/lib/x86_64-linux-gnu/liblber-2.4.so.2
COPY --from=builder /usr/lib/x86_64-linux-gnu/libsasl2.so.2 /usr/lib/x86_64-linux-gnu/libsasl2.so.2
COPY --from=builder /usr/lib/x86_64-linux-gnu/libgssapi.so.3 /usr/lib/x86_64-linux-gnu/libgssapi.so.3
COPY --from=builder /usr/lib/x86_64-linux-gnu/libgnutls.so.30 /usr/lib/x86_64-linux-gnu/libgnutls.so.30
COPY --from=builder /usr/lib/x86_64-linux-gnu/libheimntlm.so.0 /usr/lib/x86_64-linux-gnu/libheimntlm.so.0
COPY --from=builder /usr/lib/x86_64-linux-gnu/libkrb5.so.26 /usr/lib/x86_64-linux-gnu/libkrb5.so.26
COPY --from=builder /usr/lib/x86_64-linux-gnu/libasn1.so.8 /usr/lib/x86_64-linux-gnu/libasn1.so.8
COPY --from=builder /usr/lib/x86_64-linux-gnu/libcom_err.so.2 /usr/lib/x86_64-linux-gnu/libcom_err.so.2
COPY --from=builder /usr/lib/x86_64-linux-gnu/libhcrypto.so.4 /usr/lib/x86_64-linux-gnu/libhcrypto.so.4
COPY --from=builder /usr/lib/x86_64-linux-gnu/libroken.so.18 /usr/lib/x86_64-linux-gnu/libroken.so.18
COPY --from=builder /usr/lib/x86_64-linux-gnu/libp11-kit.so.0 /usr/lib/x86_64-linux-gnu/libp11-kit.so.0
COPY --from=builder /usr/lib/x86_64-linux-gnu/libidn2.so.0 /usr/lib/x86_64-linux-gnu/libidn2.so.0
COPY --from=builder /usr/lib/x86_64-linux-gnu/libunistring.so.2 /usr/lib/x86_64-linux-gnu/libunistring.so.2
COPY --from=builder /usr/lib/x86_64-linux-gnu/libtasn1.so.6 /usr/lib/x86_64-linux-gnu/libtasn1.so.6 
COPY --from=builder /usr/lib/x86_64-linux-gnu/libnettle.so.7 /usr/lib/x86_64-linux-gnu/libnettle.so.7
COPY --from=builder /usr/lib/x86_64-linux-gnu/libhogweed.so.5 /usr/lib/x86_64-linux-gnu/libhogweed.so.5
COPY --from=builder /usr/lib/x86_64-linux-gnu/libgmp.so.10 /usr/lib/x86_64-linux-gnu/libgmp.so.10
COPY --from=builder /usr/lib/x86_64-linux-gnu/libwind.so.0 /usr/lib/x86_64-linux-gnu/libwind.so.0
COPY --from=builder /usr/lib/x86_64-linux-gnu/libheimbase.so.1 /usr/lib/x86_64-linux-gnu/libheimbase.so.1
COPY --from=builder /usr/lib/x86_64-linux-gnu/libhx509.so.5 /usr/lib/x86_64-linux-gnu/libhx509.so.5
COPY --from=builder /usr/lib/x86_64-linux-gnu/libsqlite3.so.0 /usr/lib/x86_64-linux-gnu/libsqlite3.so.0
COPY --from=builder /usr/lib/x86_64-linux-gnu/libcrypt.so.1 /usr/lib/x86_64-linux-gnu/libcrypt.so.1
COPY --from=builder /usr/lib/x86_64-linux-gnu/libffi.so.7 /usr/lib/x86_64-linux-gnu/libffi.so.7
COPY --from=builder /lib/x86_64-linux-gnu/libcrypt.so.1 /lib/x86_64-linux-gnu/libcrypt.so.1

# postgresql-client
COPY --from=builder /usr/bin/psql /usr/bin/psql
COPY --from=builder /usr/lib/postgresql /usr/lib/postgresql
COPY --from=builder /usr/bin/ldd /usr/bin/ldd
COPY --from=builder /usr/lib/x86_64-linux-gnu/libpq.so.5 /usr/lib/x86_64-linux-gnu/libpq.so.5
COPY --from=builder /usr/lib/x86_64-linux-gnu/libedit.so.2 /usr/lib/x86_64-linux-gnu/libedit.so.2
COPY --from=builder /usr/lib/x86_64-linux-gnu/libgssapi_krb5.so.2 /usr/lib/x86_64-linux-gnu/libgssapi_krb5.so.2
COPY --from=builder /usr/lib/x86_64-linux-gnu/libbsd.so.0 /usr/lib/x86_64-linux-gnu/libbsd.so.0
COPY --from=builder /usr/lib/x86_64-linux-gnu/libkrb5.so.3 /usr/lib/x86_64-linux-gnu/libkrb5.so.3
COPY --from=builder /usr/lib/x86_64-linux-gnu/libk5crypto.so.3 /usr/lib/x86_64-linux-gnu/libk5crypto.so.3
COPY --from=builder /usr/lib/x86_64-linux-gnu/libkrb5support.so.0 /usr/lib/x86_64-linux-gnu/libkrb5support.so.0
COPY --from=builder /usr/lib/x86_64-linux-gnu/libkeyutils.so.1 /usr/lib/x86_64-linux-gnu/libkeyutils.so.1  

# /opt and /home
COPY --from=builder /opt/venv /opt/venv
COPY --from=builder --chown=nonroot:nonroot /opt/lemur /opt/lemur
COPY --from=builder --chown=nonroot:nonroot /home/lemur /home/lemur

# /entrypoint
COPY ./files/entrypoint /

# supervisord
COPY ./files/supervisor.conf /
COPY --from=builder /usr/bin/supervisord /usr/bin/supervisord

ENTRYPOINT ["/entrypoint"]

CMD ["/usr/bin/supervisord", "-c", "/supervisor.conf"]
