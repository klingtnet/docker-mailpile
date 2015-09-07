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
docker run --name mailpile --volumes-from mailpile-data -p 10080:10800 --rm -it klingtdotnet/mailpile
```

### Import GPG/PGP keys

TODO: fix this

```sh
gpg --export-secret-key --armor > secret.key
docker run --rm --volumes-from mailpile-data -v "$PWD"/secret.key:/secret.key -it mailpile /usr/bin/gpg --import /secret.key
rm secret.key
```

### as SystemD Service

...
