FROM alpine:3.9

RUN apk --no-cache add socat


ENTRYPOINT ["socat"]
CMD ["TCP4-LISTEN:853,reuseaddr,fork", "SOCKS4A:tor:dns4torpnlfs2ifuz2s2yf3fc7rdmsbhm6rw75euj35pac6ap25zgqad.onion:853,socksport=9050"]

