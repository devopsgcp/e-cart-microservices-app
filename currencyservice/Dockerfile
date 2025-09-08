# FROM node:20.18.1-alpine@sha256:f25b0e9d3d116e267d4ff69a3a99c0f4cf6ae94eadd87f1bf7bd68ea3ff0bef7 as base
FROM node:20.18.1-bullseye-slim as base

FROM base as builder

# Install deps for post-install scripts
RUN apt-get update && apt-get install -y \
    python3 \
    make \
    g++ \
 && rm -rf /var/lib/apt/lists/*

WORKDIR /usr/src/app

COPY package*.json ./

RUN npm install --only=production

FROM base as without-grpc-health-probe-bin

WORKDIR /usr/src/app

COPY --from=builder /usr/src/app/node_modules ./node_modules
COPY . .

EXPOSE 7000

ENTRYPOINT [ "node", "server.js" ]

FROM without-grpc-health-probe-bin

# renovate: datasource=github-releases depName=grpc-ecosystem/grpc-health-probe
ENV GRPC_HEALTH_PROBE_VERSION=v0.4.18
RUN apt-get update && apt-get install -y wget \
 && wget -qO/bin/grpc_health_probe https://github.com/grpc-ecosystem/grpc-health-probe/releases/download/${GRPC_HEALTH_PROBE_VERSION}/grpc_health_probe-linux-amd64 \
 && chmod +x /bin/grpc_health_probe \
 && apt-get remove -y wget \
 && rm -rf /var/lib/apt/lists/*
