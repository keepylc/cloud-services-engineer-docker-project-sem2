FROM golang:1.26-alpine AS builder

WORKDIR /app

COPY go.mod go.sum ./
RUN go mod download

COPY . .
RUN CGO_ENABLED=0 GOOS=linux \
    go build -trimpath -ldflags="-s -w" -o /app/server ./cmd/api

FROM alpine:3.24

RUN apk upgrade --no-cache \
    && addgroup -S app && adduser -S -u 10001 -G app app

WORKDIR /app
COPY --from=builder /app/server /app/server

USER 10001:10001

EXPOSE 8081

HEALTHCHECK --interval=30s --timeout=5s --start-period=5s --retries=3 \
    CMD wget -qO- http://localhost:8081/health || exit 1

ENTRYPOINT ["/app/server"]
