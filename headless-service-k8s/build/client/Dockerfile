FROM golang:1.23.4-alpine as build

WORKDIR /app

RUN GRPC_HEALTH_PROBE_VERSION=v0.3.2 && \
    wget -qO/bin/grpc_health_probe https://github.com/grpc-ecosystem/grpc-health-probe/releases/download/${GRPC_HEALTH_PROBE_VERSION}/grpc_health_probe-linux-amd64 && \
    chmod +x /bin/grpc_health_probe

COPY go.mod go.sum ./
RUN go mod download

COPY . .

RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -o main ./

FROM alpine:3

RUN apk add --no-cache tcpdump

COPY --from=build /app/main /
COPY --from=build /bin/grpc_health_probe /bin

ENTRYPOINT [ "./main" ]