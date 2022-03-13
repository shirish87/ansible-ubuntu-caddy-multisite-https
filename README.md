# ansible-ubuntu-caddy-multisite-https

> NOTE: NOT FOR PRODUCTION USE

Demo repository for quickly setting up multiple LetsEncrypt-based HTTPS sites with Caddy and `dns.providers.cloudflare` plugin.

This setup can be used for obtaining HTTPS certificates for non-public IPs with public domain names registered on Cloudflare DNS (e.g. `lan.example1.com` that resolves to `192.168.0.2`).

> Target machine: Ubuntu

> Requires: ansible-galaxy collection install community.docker



### Installs:

* docker, docker-compose
* golang
* [fnm](https://github.com/Schniz/fnm): nodejs LTS, yarn
* Caddy with `dns.providers.cloudflare` package



## Structure of Caddyfiles

* Global default Caddyfile lives in `/etc/caddy/Caddyfile`
* Each site (role example1, example2):
  - Has `/etc/caddy/conf.d/{{ domain }}` as Caddyfile
  - Installs an `import` in the default Caddyfile at BOF in `/etc/caddy/Caddyfile`
  - Has separate TLS, Cloudflare DNS config
  - Auto installs and updates LetsEncrypt HTTPs certificates for `{{ domain }}` and `{{ naked_domain }}`
  - Redirects:
    + HTTP to HTTPS
    + naked domain to `www` subdomain: `{{ naked_domain }}` to `{{ domain }}`
  - Serves static files in `/var/www/{{ domain }}` with Caddy `file_server`



## Building caddy from source

(Not included in the Ansible script)

Environment for the `deploy` user supports building `go` apps from source. Copy the `caddy` binary to `/usr/bin/caddy` after the build completes successfully.

```
export CADDY_VERSION=v2.5.0-beta.1
xcaddy build --with github.com/caddy-dns/cloudflare
```

This is only mentioned here because of a bug in Caddy v2.4.6 that breaks `file_server` directory listing for folders containing symlinks. Fixed in later versions.
