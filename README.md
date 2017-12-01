# Sorttv
Dockerfile for Sorttv by Cliffe

# How to use
The simplest use case is to just run the docker image using the built in (default) config file:
```
docker run --rm -it rbtr/sorttv
```

To specify your own config file, bind it in as a volume:
```
docker run --rm -it -v /path/to/my/config:/sorttv/sorttv.conf rbtr/sorttv
```
Use this same pattern to bind source and destination directories for the sort, as necessary.

To instead configure via cli flags, append those to your `docker run`:
```
docker run --rm -it rbtr/sorttv --dry-run
```

### Linux E2E Example
If you have an `unsorted` directory where media is continuously added, and a `tv` directory where it should be sorted to automatically, this can all be wired up together with some `systemd` tricks to provide a hands-off autosorting pipeline:

```
# /etc/systemd/system/sorttv.path
# ====
[Unit]
Description=Sort tv when files are added

[Path]
DirectoryNotEmpty=/unsorted

[Install]
WantedBy=default.target
```

```
[Unit]
Description=Sorttv service

[Service]
type=simple
ExecStart=/bin/bash -c "docker run --rm -v /unsorted:/unsorted:z -v /tv:/tv:z rbtr/sorttv --directory-to-sort=/unsorted --tv-directory=/tv"

# We could use a config file here instead of cli flags as
# ExecStart=... -v /path/to/config:/sorttv/sorttv.conf ...
```
