# your-dns
A docker-compose file to provide a secure adblocking DNS server

## Goal

Run a secure DoT (DNS-over-TLS) and DoH (DNS-over-HTTPS) DNS server that
can do ad blocking and hide your DNS query from your ISP.

AND

Hide your DNS query from upstream recursive DNS server.

## All components in this stack

![overview of components](https://g.gravizo.com/source/svg?https://raw.githubusercontent.com/Wh1t3Fox/your-dns/master/graph.dot)

1. [Nginx](https://www.nginx.com): High Performance HTTP Server
   that provides DNS-over-TLS and access to Pihole
   ([doc](https://docs.nginx.com/))
1. [Pihole](https://pi-hole.net): Ad blocking DNS server. Pihole forked
   dnsmasq and provide a nice UI to manage the DNS server.
   ([donate](https://pi-hole.net/donate/))
1. [Stubby](https://dnsprivacy.org/wiki/display/DP/DNS+Privacy+Daemon+-+Stubby):
   A DNS stub server, which support forwarding DNS request to upstream
   DNS-over-TLS server. Note Unbound also support forwarding request to
   upstream over TLS, but I was told (can't find the reference) Unbound
   does not reuse TLS connections which is a concern to me (my ATT
   gateway has an internal NAT table with limited # of entries).
   ([doc](https://dnsprivacy.org/wiki/display/DP/Configuring+Stubby))
1. [DNS-over-HTTPS](https://github.com/m13253/dns-over-https): A DoH
   server.

## Prerequisites

1. Install Docker ([how](https://docs.docker.com/v17.12/install/)) and
   `docker-compose` command
   ([how](https://docs.docker.com/compose/install/)).
1. Know how to DNAT from your public IP to the server running the stack.
   Or alternatively if you have IPv6, allow dport=853 access to your
   server.
1. Know how to get a Let's Encrypt certificate for your domain. You need
   a single wildcard certificate if you host both DoH server and pihole
   on the same server.

## Run the stack

The following instruction will run a list of jobs on docker to
DNS-over-TLS service on port 853 and foward your request through PiHole
then to Cloudflare DNS over Tor.

1. Modify `.env` file. See the comment in that file for instructions.
2. Create a `letsencrypt` directory in the config directory, create a 
  `credentials.txt` file with your
   Cloudflare API key (See
   https://certbot-dns-cloudflare.readthedocs.io/en/stable/#credentials
   for reference)
3. Run the following command which should success.
```
docker-compose run --entrypoint="certbot certonly --email ${YOUR_EMAIL:?} -d *.${DOMAIN_NAME:?},${DOMAIN_NAME:?} --rsa-key-size=4096 --agree-tos --force-renewal --dns-cloudflare-credentials /credentials.txt --dns-cloudflare" certbot
```
4. Run `docker-compose up -d`
