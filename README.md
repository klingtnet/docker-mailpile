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

### as SystemD Service

...
