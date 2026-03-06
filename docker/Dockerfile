# syntax=docker/dockerfile:1.4

# --- First stage
FROM --platform=$BUILDPLATFORM golang:1.25-alpine@sha256:8e02eb337d9e0ea459e041f1ee5eece41cbb61f1d83e7d883a3e2fb4862063fa AS build

ARG REPOSITORY=chainguard-forks
ARG PLUGIN=velero-plugin-for-openstack
ARG PKG=github.com/chainguard-forks/velero-plugin-for-openstack
ARG VERSION=0.0.0
ARG GIT_SHA=nil

ARG TARGETOS
ARG TARGETARCH
ARG TARGETVARIANT

ARG GOOS=linux
ARG GOARCH=amd64

ENV GOOS=${TARGETOS}
ENV GOARCH=${TARGETARCH}
ENV GOARM=${TARGETVARIANT}

ENV GOPROXY=https://proxy.golang.org


WORKDIR /go/src/github.com/${REPOSITORY}/${PLUGIN}
COPY . .
RUN export LDFLAGS="-X ${PKG}/src/utils.Version=${VERSION} -X ${PKG}/src/utils.GitSHA=${GIT_SHA}" \
    export GOARM=$( echo "${GOARM}" | cut -c2-) && \
    CGO_ENABLED=0 \
      go build \
      -ldflags "${LDFLAGS}" \
      -o /go/bin/${PLUGIN} \
      .

# --- Second stage
FROM alpine:3@sha256:25109184c71bdad752c8312a8623239686a9a2071e8825f20acb8f2198c3f659

ARG PLUGIN=velero-plugin-for-openstack
RUN mkdir /plugins
COPY --from=build /go/bin/${PLUGIN} /plugins/
USER nobody:nogroup
ENTRYPOINT ["cp", "-rT", "/plugins/", "/target/"]
