FROM docker.io/bitnami/minideb:buster
LABEL maintainer "Bitnami <containers@bitnami.com>"

ENV OS_ARCH="amd64" \
    OS_FLAVOUR="debian-10" \
    OS_NAME="linux"

COPY prebuildfs /
# Install required system packages and dependencies
RUN install_packages acl ca-certificates curl gzip libc6 libgeoip1 libpcre3 libssl1.1 procps tar zlib1g
RUN . /opt/bitnami/scripts/libcomponent.sh && component_unpack "nginx" "1.18.0-5" --checksum 853ef167b701384933b7781f74c7cbb2490352dc8c3fc085eccb064763a8fd01
RUN . /opt/bitnami/scripts/libcomponent.sh && component_unpack "render-template" "1.0.0-2" --checksum f1bee333423f6627d795ea9e0b6a89c18d2e5c2e2d015bb4620f040f1d520cf6
RUN . /opt/bitnami/scripts/libcomponent.sh && component_unpack "kubeapps" "1.11.3-0" --checksum cfd944766b405a9a405eae13fb69c8ef1d403013c002cf7db4eea5931b3a7100
RUN . /opt/bitnami/scripts/libcomponent.sh && component_unpack "gosu" "1.12.0-2" --checksum 4d858ac600c38af8de454c27b7f65c0074ec3069880cb16d259a6e40a46bbc50
RUN apt-get update && apt-get upgrade -y && \
    rm -r /var/lib/apt/lists /var/cache/apt/archives
RUN ln -sf /dev/stdout /opt/bitnami/nginx/logs/access.log
RUN ln -sf /dev/stderr /opt/bitnami/nginx/logs/error.log

COPY rootfs /
RUN rm -rf /app && \
    ln -sf /opt/bitnami/kubeapps /opt/bitnami/kubeapps-dashboard && \
    mv /opt/bitnami/kubeapps/build /app
RUN chmod -R g+rwX /opt/bitnami/nginx/conf
RUN /opt/bitnami/scripts/nginx/postunpack.sh
ENV BITNAMI_APP_NAME="kubeapps-dashboard" \
    BITNAMI_IMAGE_VERSION="1.11.3-debian-10-r74" \
    NGINX_ENABLE_CUSTOM_PORTS="no" \
    NGINX_HTTPS_PORT_NUMBER="" \
    NGINX_HTTP_PORT_NUMBER="" \
    PATH="/opt/bitnami/nginx/sbin:/opt/bitnami/common/bin:$PATH"

EXPOSE 8080 8443

WORKDIR /app
USER 1001
ENTRYPOINT [ "/opt/bitnami/scripts/nginx/entrypoint.sh" ]
CMD [ "/opt/bitnami/scripts/nginx/run.sh" ]
