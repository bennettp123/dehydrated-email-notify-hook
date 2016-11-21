# dehydrated-email-notify-hook

This is a semi-automated hook for
[dehydrated](https://github.com/lukas2511/dehydrated) that notifies you by
email when a new DNS record needs to be created or updated. This is useful
when your DNS provider has no API and does not support dynamic DNS updates, so
record creation needs to be done manually. With this hook, you will be
notified when a new challenge is ready to be deployed.

When using this hook, dehydrated will generate a new certificate and wait for
you to manually update the challenge record with your DNS provider of choice.
If desired, you can schedule dehydrated using cron, and still be notified when
your certificate is about to expire.

This should only be used if your DNS provider does not support automation. For
a fully automated solution, consider one of the other options 
[here](https://github.com/lukas2511/dehydrated/wiki/Examples-for-DNS-01-hooks).

## Setup

```
git clone https://github.com/lukas2511/dehydrated
cd dehydrated
git clone https://github.com/bennettp123/letsencrypt.sh-email-notify-hook hooks/email-notify
```

## Usage

If RECIPIENT is set, the email will be sent to the email address specified. 
Otherwise, it will be sent to the current user.

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
 + Requesting challenge for example.com...
 + Settling down for 10s...
```

Check your email and create the TXT record specified. For best result, use the
smallest TTL allowed by your DNS provider.

dehydrated will wait indefinitely for you to create the TXT record, and for it
to propagate (this may take a while):

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

When complete, a second email will be sent notifying you that the TXT record
can be removed.

Finally, if you plan to enable ocsp stapling behind a proxy, see
[here](HOWTO-NGINX-OCSP-STAPLING-BEHIND-PROXY.md).

