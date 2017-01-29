FROM alpine:3.5

LABEL com.github.pottava.application="aws-s3-proxy" \
      com.github.pottava.usage="docker run -d -p 8080:80 -e AWS_S3_BUCKET pottava/s3-proxy" \
      com.github.pottava.license="MIT"

ENV AWS_REGION=us-east-1 \
    APP_PORT=80

RUN apk add --no-cache ca-certificates

RUN apk --no-cache add --virtual build-dependencies bash gcc musl-dev openssl go git \

    # Install go 1.8
    && GOLANG_VERSION=1.8rc3 \
    && GOLANG_SRC_URL=https://golang.org/dl/go$GOLANG_VERSION.src.tar.gz \
    && GOLANG_SRC_SHA256=38b1c1738f111f7bccdd372efca2aa98a7bad1ca2cb21767ba69f34ae007499c \
    && export GOROOT_BOOTSTRAP="$(go env GOROOT)" \
    && wget -q "$GOLANG_SRC_URL" -O golang.tar.gz \
    && echo "$GOLANG_SRC_SHA256  golang.tar.gz" | sha256sum -c - \
    && tar -C /usr/local -xzf golang.tar.gz \
    && wget -q https://raw.githubusercontent.com/docker-library/golang/master/1.8/alpine/no-pic.patch \
    && cd /usr/local/go/src \
    && patch -p2 -i /no-pic.patch \
    && ./make.bash \
    && export GOPATH=/go \
    && mkdir -p /go/src /go/bin \
    && chmod -R 777 /go \

    # Compile s3-proxy
    && go get -u github.com/pottava/aws-s3-proxy \
    && mv /go/bin/aws-s3-proxy /aws-s3-proxy \

    # Clean up
    && apk del --purge build-dependencies \
    && rm -rf /usr/local/go /usr/lib/go /go /golang.tar.gz /*.patch

EXPOSE 80

CMD ["/aws-s3-proxy"]