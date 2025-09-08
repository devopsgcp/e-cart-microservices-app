FROM node:20.18.1-bullseye-slim as base
FROM base as builder

# Install dependencies for native modules
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

# Install gRPC health probe
ENV GRPC_HEALTH_PROBE_VERSION=v0.4.18
RUN apt-get update && apt-get install -y wget && rm -rf /var/lib/apt/lists/* && \
    wget -qO/bin/grpc_health_probe https://github.com/grpc-ecosystem/grpc-health-probe/releases/download/${GRPC_HEALTH_PROBE_VERSION}/grpc_health_probe-linux-amd64 && \
    chmod +x /bin/grpc_health_probe
