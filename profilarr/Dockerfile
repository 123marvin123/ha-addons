FROM node:18-bullseye as builder

ARG PROFILARR_VERSION
ARG BUILD_FROM
ARG BUILD_ARCH

RUN echo "Profilarr version: ${PROFILARR_VERSION}" && \
    echo "Build architecture: ${BUILD_ARCH}" && \
    echo "Build from: ${BUILD_FROM}"

RUN apt-get update && apt-get install -y wget tar
RUN wget https://github.com/Dictionarry-Hub/profilarr/archive/refs/tags/v${PROFILARR_VERSION}.tar.gz
RUN tar -xvf v${PROFILARR_VERSION}.tar.gz
WORKDIR /profilarr-${PROFILARR_VERSION}/frontend

RUN npm ci && npm run build
RUN cd .. && \
    mkdir -p dist/backend dist/static && \
    cp -r frontend/dist/* dist/static/ && \
    cp -r backend/* dist/backend/ && \
    cp backend/requirements.txt dist/

FROM $BUILD_FROM

ARG PROFILARR_VERSION
ARG BUILD_ARCH

RUN echo "Profilarr version: ${PROFILARR_VERSION}" && \
    echo "Build architecture: ${BUILD_ARCH}"

# Install dependencies
RUN apt-get update && apt-get install -y python3 python3-pip && \
    pip3 install --no-cache-dir -r /dist/requirements.txt && \
    apt-get remove -y python3-pip && apt-get autoremove -y

RUN apt-get clean && rm -rf /var/lib/apt/lists/*

WORKDIR /usr/bin/app

# Copy Profilarr
COPY --from=builder /profilarr-${PROFILARR_VERSION}/dist .

# Copy root filesystem
COPY rootfs /
