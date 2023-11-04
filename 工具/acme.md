# Automatic Certificate Management Environment (ACME)

https://www.rfc-editor.org/rfc/rfc8555.html

# certbot

## manage account

- register --email EMAIL
- update_account --email EMAIL
- unregister --email EMAIL

## manage certificates

- certificates
- revoke
- delete

## enhance

- -n, --non-interactive
- -d DOMAIN, --domain DOMAIN

## certonly

- -n, --non-interactive
- -d DOMAIN, --domain DOMAIN
- --key-type
- --manual
- --dns-cloudflare

```shell
certbot certonly --manual --domain '*.xxx' --key-type=ecdsa --cert-name=xxx
```

## run

## renew
