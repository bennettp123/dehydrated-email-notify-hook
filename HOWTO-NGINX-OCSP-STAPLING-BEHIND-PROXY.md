# NGINX OCSP stapling file

If you want to enable OCSP stapling in nginx behind a proxy server, you need 
to provide a stapling file. email-notify-hook can optionally generate this 
file after generating the certificate.

This is only needed if direct access to the internet is not available.

To use this, set OCSP_RESPONSE_FILE to the file to store the OCSP response.
Optionally, you can also set http_proxy, and the response will be obtained
via the proxy specified.

```
export OCSP_RESPONSE_FILE=/path/to/ocsp.resp
export http_proxy=http://127.0.0.1:3128
./dehydrated --cron --domain example.com --challenge dns-01 --hook 'hooks/email-notify/hook.sh'
```

To enable this in nginx, add the following line to nginx config:
```
ssl_stapling_file /home/letsencrypt/ocsp/ocsp.resp;
```

You should also update the OCSP file regularly (eg daily using cron) to ensure
it is valid and up to date.

