FROM golang:1.17.4-alpine3.15@sha256:d8bd35607c405fcef71d749aa367f86954706c3a57a602fb0bcaae3581043f8f as builder

RUN apk --no-cache add gcc musl-dev make && go get github.com/mailru/easyjson && go install github.com/mailru/easyjson/...@latest

COPY . /src

ARG VERSION=dev

RUN cd /src && make generate && go build -o /src/bin/proxy -ldflags "-s -w -X main.version=$VERSION"

FROM alpine:3.15@sha256:ecbdce53b2c2f43ab1b19418bcbd3f120a23547d9497030c8d978114512b883e

RUN adduser -g "proxy" -D -H proxy proxy

RUN apk --no-cache add ca-certificates \
    && rm -rf /var/cache/apk/*

COPY --from=builder /src/bin/proxy /bin/proxy

USER proxy

EXPOSE 8080

ENTRYPOINT ["/bin/proxy"]
