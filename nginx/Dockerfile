# This Dockerfiles is based on https://github.com/kyos0109/nginx-distroless/blob/master/Dockerfile
FROM nginx:1.18.0 as base

RUN mkdir -p /opt/var/cache/nginx && \
    cp -a --parents /usr/lib/nginx /opt && \
    cp -a --parents /usr/share/nginx /opt && \
    cp -a --parents /var/log/nginx /opt && \
    cp -aL --parents /var/run /opt && \
    cp -a --parents /etc/nginx /opt && \
    cp -a --parents /etc/passwd /opt && \
    cp -a --parents /etc/group /opt && \
    cp -a --parents /usr/sbin/nginx /opt && \
    cp -a --parents /usr/sbin/nginx-debug /opt && \
    cp -a --parents /lib/x86_64-linux-gnu/ld-* /opt && \
    cp -a --parents /lib/x86_64-linux-gnu/libpcre.so.* /opt && \
    cp -a --parents /lib/x86_64-linux-gnu/libz.so.* /opt && \
    cp -a --parents /lib/x86_64-linux-gnu/libc* /opt && \
    cp -a --parents /lib/x86_64-linux-gnu/libdl* /opt && \
    cp -a --parents /lib/x86_64-linux-gnu/libpthread* /opt && \
    cp -a --parents /lib/x86_64-linux-gnu/libcrypt* /opt && \
    cp -a --parents /usr/lib/x86_64-linux-gnu/libssl.so.* /opt && \
    cp -a --parents /usr/lib/x86_64-linux-gnu/libcrypto.so.* /opt && \
    cp /usr/share/zoneinfo/${TIME_ZONE:-ROC} /opt/etc/localtime && \
    rm /opt/etc/nginx/conf.d/default.conf

FROM gcr.io/distroless/base-debian10

COPY --from=base /opt /
COPY files/lemur-http.conf /etc/nginx/conf.d/lemur-http.conf

ENTRYPOINT ["nginx", "-g", "daemon off;"]
