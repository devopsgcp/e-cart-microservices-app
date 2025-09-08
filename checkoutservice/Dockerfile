# Copyright 2020 Google LLC
# Licensed under the Apache License, Version 2.0

# Use Go 1.22.2 with Alpine for building
FROM golang:1.23.4-alpine@sha256:c23339199a08b0e12032856908589a6d41a0dab141b8b3b21f156fc571a3f1d3 AS builder
RUN go version
RUN apk add --no-cache ca-certificates git build-base

WORKDIR /src

# Restore dependencies
COPY go.mod go.sum ./
RUN go mod download

# Copy the rest of the source
COPY . .

# Skaffold passes in debug-oriented compiler flags
ARG SKAFFOLD_GO_GCFLAGS
RUN go build -gcflags="${SKAFFOLD_GO_GCFLAGS}" -o /checkoutservice .

# Minimal runtime image without grpc_health_probe
FROM alpine:3.18.0@sha256:02bb6f428431fbc2809c5d1b41eab5a68350194fb508869a33cb1af4444c9b11 AS without-grpc-health-probe-bin
RUN apk add --no-cache ca-certificates

WORKDIR /src
COPY --from=builder /checkoutservice /src/checkoutservice

# For Skaffold debug
ENV GOTRACEBACK=single

EXPOSE 5050
ENTRYPOINT ["/src/checkoutservice"]

# Final image with grpc_health_probe
FROM without-grpc-health-probe-bin AS final
ENV GRPC_HEALTH_PROBE_VERSION=v0.4.18
RUN wget -qO /bin/grpc_health_probe \
    https://github.com/grpc-ecosystem/grpc-health-probe/releases/download/${GRPC_HEALTH_PROBE_VERSION}/grpc_health_probe-linux-amd64 && \
    chmod +x /bin/grpc_health_probe
