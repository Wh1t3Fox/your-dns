FROM alpine:3.9

RUN apk add --no-cache tor

USER tor
CMD ["tor", "-f", "/etc/tor/torrc"]
