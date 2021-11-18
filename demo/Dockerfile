# syntax=docker/dockerfile:1.3

ARG GO_VERSION=1.17
ARG GORELEASER_XX_VERSION=1.0.0

# gorelease-xx is a cross-compilation helper for your Go project
FROM --platform=$BUILDPLATFORM crazymax/goreleaser-xx:${GORELEASER_XX_VERSION} AS goreleaser-xx

FROM --platform=$BUILDPLATFORM golang:${GO_VERSION}-alpine AS base
COPY --from=goreleaser-xx / /
RUN apk add --no-cache git
WORKDIR /src

FROM base AS build
ARG TARGETPLATFORM
ARG GIT_REF
RUN --mount=type=bind,source=.,target=/src,rw \
  --mount=type=cache,target=/root/.cache/go-build \
  --mount=type=cache,target=/go/pkg/mod \
  goreleaser-xx --debug \
    --name="lorrainejug-buildx-bake" \
    --dist="/out" \
    --hooks="go mod tidy" \
    --hooks="go mod download" \
    --main="." \
    --ldflags="-s -w -X 'main.version={{.Version}}' -X main.commit={{.Commit}} -X main.date={{.Date}}" \
    --files="LICENSE" \
    --files="README.md"

FROM scratch AS artifact
COPY --from=build /out/*.tar.gz /
COPY --from=build /out/*.zip /

FROM alpine

RUN apk --update --no-cache add \
    ca-certificates \
    openssl \
    shadow \
  && addgroup -g 1000 lorrainejug \
  && adduser -u 1000 -G lorrainejug -s /sbin/nologin -D lorrainejug

COPY --from=build /usr/local/bin/lorrainejug-buildx-bake /usr/local/bin/lorrainejug-buildx-bake
RUN lorrainejug-buildx-bake --version

USER lorrainejug
EXPOSE 8080
ENTRYPOINT [ "lorrainejug-buildx-bake" ]
