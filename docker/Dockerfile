FROM ubuntu:22.04

ARG DEBIAN_FRONTEND=noninteractive
ARG WLA_DX_COMMIT=8fb00753aa633b1e5ccabf502037e23b334d5d82

ENV OPT=/opt \
    WORKSPACE=/opt/workspace

RUN apt-get --allow-releaseinfo-change update \
    && apt-get install -y --no-install-recommends \
        bison \
        build-essential \
        ca-certificates \
        cmake \
        curl \
        diffstat \
        flex \
        gawk \
        git \
        libsdl1.2-dev \
        make \
        sudo \
        unzip \
        wget \
    && rm -rf /var/lib/apt/lists/*

# Build wla-dx at the pinned revision used by this project.
RUN git init /tmp/wla-dx \
    && cd /tmp/wla-dx \
    && git remote add origin https://github.com/vhelin/wla-dx.git \
    && git fetch --depth 1 origin "${WLA_DX_COMMIT}" \
    && git checkout --detach FETCH_HEAD \
    && cmake -G "Unix Makefiles" . \
    && make -j"$(nproc)" \
    && install -m 0755 binaries/* /usr/local/bin/ \
    && rm -rf /tmp/wla-dx

# Build nesasm from source.
RUN git clone --depth 1 https://github.com/camsaul/nesasm.git /tmp/nesasm \
    && make -C /tmp/nesasm/source \
    && make -C /tmp/nesasm/source install \
    && rm -rf /tmp/nesasm

RUN groupadd -r builder \
    && useradd -m -d /home/builder -g builder -G dialout,sudo builder \
    && echo "builder:builder" | chpasswd \
    && echo '%sudo ALL=(ALL) NOPASSWD:ALL' > /etc/sudoers.d/builder \
    && chmod 0440 /etc/sudoers.d/builder \
    && mkdir -p "${WORKSPACE}" \
    && chown -R builder:builder "${WORKSPACE}"

USER builder
WORKDIR ${WORKSPACE}

RUN { \
        echo 'alias status="git status"'; \
        echo 'alias checkout="git checkout"'; \
        echo 'alias branch="git branch"'; \
        echo 'alias branches="git branch -a"'; \
        echo 'alias clone="git clone"'; \
        echo 'alias commit="git commit -m "'; \
        echo 'alias log="git log"'; \
        echo 'alias submod="git submodule update --init --recursive"'; \
        echo 'alias pull="git pull"'; \
        echo 'alias fund="find ./ | grep"'; \
    } >> /home/builder/.bashrc

CMD ["bash"]
