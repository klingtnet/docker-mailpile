# Mailpile Dockerized

## Quickstart

- generate data container

```sh
docker create --name mailpile-data -v /home/mpuser/.gnupg -v /home/mpuser/.local/share/Mailpile klingtdotnet/mailpile
```

- run the container
- mailpile will ask you to enter your new password on first run
- make sure that nobody can access the web ui at this moment, g.e. by exposing the port on localhost: `-v 127.0.0.1:10080:10080`
- if you work remotely, then bind the remote port to your local machine: `ssh -L 12345:localhost:10800 some_server`

```sh
docker run --name mailpile --volumes-from mailpile-data -p 10080:10080 --rm -it klingtdotnet/mailpile
```

### Import GPG/PGP keys

TODO: fix this

```sh
gpg --export-secret-key --armor > secret.key
docker run --rm --volumes-from mailpile-data -v "$PWD"/secret.key:/secret.key -it klingtdotnet/mailpile /usr/bin/gpg --import /secret.key
rm secret.key
```

### as SystemD Service

- copy `mailpile@.service` to `/etc/systemd/system` and `mailpile` environment file (edit if necessary) to `/etc/sysconfig`
- `systemctl daemon-reload`
- `systemctl start mailpile@someuser`
- to run it automatically on boot `systemctl enable mailpile@someuser`

## nginx Reverse Proxy Config

- see [official docs](https://github.com/mailpile/Mailpile/wiki/Accesing-The-GUI-Over-Internet) for other webserver configurations

```nginx
server {
  listen 80;
  server_name server.com;
  return 301 https://$server_name$request_uri;
}

server {
  listen 443 ssl;
  server_name server.com;

  # see https://raymii.org/s/tutorials/Strong_SSL_Security_On_nginx.html
  # for notes on the good SSL on nginx
  ssl_certificate /etc/nginx/ssl/server.com.crt;
  ssl_certificate_key /etc/nginx/ssl/server.com.key;
  ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
  ssl_ciphers 'EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH';
  ssl_prefer_server_ciphers   on;
  ssl_session_cache shared:SSL:10m;
  add_header Strict-Transport-Security "max-age=31536000; includeSubdomains";
  ssl_dhparam /etc/nginx/ssl/dhparam.pem;

  location / {
    access_log /var/log/nginx/mailpile_access.log;
    error_log /var/log/nginx/mailpile_error.log info;

    proxy_pass http://127.0.0.1:33411;
    proxy_set_header X-Forwarded-Host $host;
    proxy_set_header X-Forwarded-Server $host;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }
}
```
