ARG RUST_VERSION=latest
FROM rust:${RUST_VERSION} AS rust-stage

FROM sektek/devcontainer-base:latest

COPY --from=rust-stage /usr/local/cargo /usr/local/cargo
COPY --from=rust-stage /usr/local/rustup /usr/local/rustup

ENV PATH=/usr/local/cargo/bin:$PATH
