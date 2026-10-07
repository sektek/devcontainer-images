ARG GOLANG_VERSION=latest
FROM golang:${GOLANG_VERSION} AS golang-stage

FROM sektek/devcontainer-base:latest

COPY --from=golang-stage /usr/local/go /usr/local/go

ENV PATH=/usr/local/go/bin:$PATH
