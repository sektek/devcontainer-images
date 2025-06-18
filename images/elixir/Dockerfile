ARG ELIXIR_VERSION=1.18

FROM elixir:${ELIXIR_VERSION} AS elixir-stage

FROM sektek/devcontainer-base:latest

COPY --from=elixir-stage /usr/local /usr/local

ENV LANG=C.UTF-8

CMD ["iex"]
