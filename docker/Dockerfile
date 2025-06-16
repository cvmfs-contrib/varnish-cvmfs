# ---------- Base image ----------
FROM almalinux:9

LABEL maintainer="vavolkl@cern.ch" \
      description="Varnish + libvmod-dynamic forward-proxy for CVMFS"

ENV VAR_SRC_DIR=/opt/src \
    VARNISH_USER=varnish \
    VARNISH_GROUP=varnish \
    VARNISH_LISTEN_PORT=6081

# ---------- System preparation ----------
RUN dnf -y update && \
    dnf -y install yum-utils && \
    dnf config-manager --set-enabled crb && \
    dnf -y install epel-release && \
    dnf -y install \
        git \
        gcc \
        gcc-c++ \
        make \
        autoconf \
        automake \
        libtool \
        pkgconfig \
        python3-docutils \
        getdns-devel \
        varnish \
        varnish-devel \
        which && \
    dnf clean all

WORKDIR ${VAR_SRC_DIR}

# ---------- Build & install libvmod-dynamic ----------
RUN git clone https://github.com/nigoroll/libvmod-dynamic.git && \
    cd libvmod-dynamic && \
    git checkout 6.6 && \ 
    ./autogen.sh && \
    ./configure && \
    make -j"$(nproc)" && \
    make install && \
    ldconfig && \
    cd .. && \
    rm -rf libvmod-dynamic

# ---------- Retrieve example VCL ----------
RUN git clone --depth 1 https://github.com/cvmfs-contrib/varnish-cvmfs.git && \
    cp varnish-cvmfs/cvmfs.vcl /etc/varnish/default.vcl && \
    rm -rf varnish-cvmfs

# ---------- Networking ----------
EXPOSE ${VARNISH_LISTEN_PORT}/tcp

# ---------- Entrypoint ----------
CMD ["bash", "-c", "\
      varnishd -F \
        -a :${VARNISH_LISTEN_PORT} \
        -f /etc/varnish/default.vcl \
        -s malloc,512m \
    "]

