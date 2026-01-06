# syntax=docker/dockerfile:1.3

# --- First stage
FROM --platform=$BUILDPLATFORM golang:1.22-alpine@sha256:1699c10032ca2582ec89a24a1312d986a3f094aed3d5c1147b19880afe40e052 AS build

ARG REPOSITORY=Lirt
ARG PLUGIN=velero-plugin-for-openstack
ARG PKG=github.com/Lirt/velero-plugin-for-openstack
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
FROM alpine:3@sha256:865b95f46d98cf867a156fe4a135ad3fe50d2056aa3f25ed31662dff6da4eb62
RUN mkdir /plugins
COPY --from=build /go/bin/${PLUGIN} /plugins/
USER nobody:nogroup
ENTRYPOINT ["cp", "-rT", "/plugins/", "/target/"]
