FROM alpine:3.21

ARG COOKCLI_VERSION=0.29.0

RUN apk add --no-cache curl \
    && curl -fsSL "https://github.com/cooklang/cookcli/releases/download/v${COOKCLI_VERSION}/cook-x86_64-unknown-linux-musl.tar.gz" \
       -o /tmp/cook.tar.gz \
    && tar -xzf /tmp/cook.tar.gz -C /usr/local/bin/ \
    && rm /tmp/cook.tar.gz \
    && apk del curl

EXPOSE 9080

CMD ["cook", "server", "--host", "0.0.0.0", "--port", "9080", "/recipes"]
