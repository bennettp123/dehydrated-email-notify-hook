# dehydrated-email-notify-hook

This is a semi-automated hook for [dehydrated](https://github.com/lukas2511/dehydrated) that notifies you by email when a new DNS record needs to be created. This is useful when your DNS provider has no API and does not support dynamic DNS updates, so record creation needs to be done manually. With this hook, you will be notified when a new challenge is ready to be deployed.

When using this hook, dehydrated will generate a new certificate and wait for you to manually update the challenge record with your DNS provider of choice. If desired, you can schedule dehydrated using cron, and still be notified when your certificate is about to expire.

This should only be used if your DNS provider does not support automation. For a fully automated solution, consider one of the other options [here](https://github.com/lukas2511/dehydrated/wiki/Examples-for-DNS-01-hooks).

## Setup

```
git clone https://github.com/lukas2511/dehydrated
cd dehydrated
git clone https://github.com/bennettp123/letsencrypt.sh-email-notify-hook hooks/email-notify
```

## Usage

If RECIPIENT is set, the email will be sent to the email address specified. Otherwise, it will be sent to the current user.

```
./dehydrated --cron --domain example.com --challenge dns-01 --hook 'hooks/email-notify/hook.sh'
#
# !! WARNING !! No main config file found, using default config!
#
+ Generating account key...
+ Registering account key with letsencrypt...
Processing example.com
 + Signing domains...
 + Creating new directory /home/letsencrypt/src/letsencrypt.sh.staging/certs/example.com ...
 + Generating private key...
 + Generating signing request...
 + Requesting challenge for foobar.alias.bennettp123.com...
 + Settling down for 10s...
```

Check your email and create the TXT record specified. Use the smallest TTL possible.

dehydrated will wait indefinitely for you to create the TXT record, and for it propagate (this may take a while):

```
 + DNS not propagated. Waiting 30s for record creation and replication...
 + DNS not propagated. Waiting 30s for record creation and replication...
 + DNS not propagated. Waiting 30s for record creation and replication...
 + Challenge is valid!
 + Requesting certificate...
 + Checking certificate...
 + Done!
 + Creating fullchain.pem...
 + Done!
 
```

When complete, a second email will be sent notifying you that the TXT record can be removed.

## OCSP stapling file

If you're behind a firewall and can only access the internet via proxy, you need to provide a stapling file. email-notify-hook can optionally generate this stapling file after generating the certificate.

This is only needed if direct access to the internet is not available.

To use this, set OCSP_RESPONSE_FILE to the file to store the OCSP response. Optionally, you can also set http_proxy, and the response will be obtained via the proxy specified.

```
export OCSP_RESPONSE_FILE=/path/to/ocsp.resp
export http_proxy=http://127.0.0.1:3128
./dehydrated --cron --domain example.com --challenge dns-01 --hook 'hooks/email-notify/hook.sh'
```

To enable this in nginx, add the following line to nginx config:
```
ssl_stapling_file /home/letsencrypt/ocsp/ocsp.resp;
```

You should also update the OCSP file regularly (eg daily using cron) to ensure it is valid and up to date.

