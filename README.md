# Mailpile Dockerized

## Quickstart

- generate data container

```sh
docker create --name mailpile-data -v /home/mpuser klingtdotnet/mailpile
```

- run the container

```sh
docker run --name mailpile --volumes-from mailpile-data -p 10080 --rm -it klingtdotnet/mailpile
```

### Import GPG/PGP keys

TODO: fix this

```sh
gpg --export-secret-key --armor > secret.key
docker run --rm --volumes-from mailpile-data -v "$PWD"/secret.key:/secret.key -u root -it mailpile /usr/bin/gpg --import /secret.key
rm secret.key
```

### as SystemD Service

...
