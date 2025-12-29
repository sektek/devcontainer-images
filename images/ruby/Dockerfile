ARG RUBY_VERSION=4.0
FROM ruby:${RUBY_VERSION} AS ruby-stage

FROM sektek/devcontainer-base:latest

COPY --from=ruby-stage /usr/local /usr/local

CMD ["irb"]
