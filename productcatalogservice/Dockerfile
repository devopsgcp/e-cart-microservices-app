# ---------- Stage 1: Build ----------
    FROM golang:1.23.4-alpine AS builder

    # Install required packages
    RUN apk add --no-cache ca-certificates git build-base
    
    WORKDIR /src
    
    # Copy dependency files first for better caching
    COPY go.mod go.sum ./
    
    # Remove any toolchain directive that may force an older Go version
    RUN sed -i '/^toolchain/d' go.mod
    
    # Download dependencies
    RUN go mod download
    
    # Copy the source code
    COPY . .
    
    # Verify Go version inside the container
    RUN go version
    
    # Skaffold passes in debug-oriented compiler flags
    ARG SKAFFOLD_GO_GCFLAGS
    RUN go build -gcflags="${SKAFFOLD_GO_GCFLAGS}" -o /productcatalogservice .
    
    # ---------- Stage 2: Base runtime without grpc_health_probe ----------
    FROM alpine:3.18.0 AS without-grpc-health-probe-bin
    RUN apk add --no-cache ca-certificates
    
    WORKDIR /src
    COPY --from=builder /productcatalogservice ./server
    COPY products.json .
    
    ENV GOTRACEBACK=single
    
    EXPOSE 3550
    ENTRYPOINT ["/src/server"]
    
    # ---------- Stage 3: Final runtime with grpc_health_probe ----------
    FROM without-grpc-health-probe-bin
    ENV GRPC_HEALTH_PROBE_VERSION=v0.4.18
    RUN wget -qO/bin/grpc_health_probe \
        https://github.com/grpc-ecosystem/grpc-health-probe/releases/download/${GRPC_HEALTH_PROBE_VERSION}/grpc_health_probe-linux-amd64 && \
        chmod +x /bin/grpc_health_probe
    