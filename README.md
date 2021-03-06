# Bind DNS Server Docker image running on Alpine Linux

## Description

This docker image provides a [bind service](https://www.isc.org/downloads/bind/) based on [Alpine Linux](https://hub.docker.com/_/alpine/).

[![](https://img.shields.io/docker/pulls/canarysat/alpine-dns)](https://img.shields.io/docker/pulls/canarysat/alpine-dns)
[![](https://img.shields.io/docker/image-size/canarysat/alpine-dns)](https://img.shields.io/docker/image-size/canarysat/alpine-dns)


## Usage

If you like to use this image, please use a specific version tag like `v1.0.0` or the branches `latest`, `stable`. The other branches are only temporary and will be deleted after the merge into the other branches.

There are two options to start your Bind container.

### Docker Run

Use this docker command to run the bind container.
```
docker run -d --name bind --publish 53:53/udp \
-v /bindconfig:/etc/bind -v /bindlog:/var/log/named \
--restart=always canarysat/alpine-dns:latest
```


### docker-compose

Check out the `docker-compose.yml` file in the [git repo](https://raw.githubusercontent.com/canarysat/alpine-dns/master/docker-compose.yml).
```
version: '3'

services:

  alpine-dns:
    image: 'canarysat/alpine-dns:stable'
    network_mode: bridge
    container_name: 'alpine-dns'
    restart: always

    ports:
      - 53:53/udp

    volumes:
      - '$PWD/bindconfig:/etc/bind'
      - '$PWD/logs/named:/var/log/named'
```

```
docker-compose up -d
```

### Docker Environment Variables

It's possible to change some variables like the uid/gid of the technical bind user/group or the executed named command. Just pass the variables via docker command to the container or define them in the docker-compose file.


#### User-ID / GroupID

Example of overwrite of the uid/gid.
```
NAMED_UID=666
NAMED_GID=666
```

or via docker-compose file:
```
    environment:
    - NAMED_UID=666
    - NAMED_GID=666
```

The default ids in the alpine bind package are:
```
UID=1000
GID=101
```

#### Named Command

It's also possible to add some additional `named` command options. Just overwrite the following variable:
```
COMMAND_OPTIONS=<option>
```

For example if you like to write all logs to the stoud, then change the variable `COMMAND` like this:
```
COMMAND_OPTIONS=-g
```


## Bind Stuff

### Bind Configuration

To pass your configuration directory with all configs and files, you've to mount the volume into the docker container with the docker option:
```
-v <bindconfig>:/etc/bind
```

Please verify, that the owner of the files is set equal to the uid/gid of the named user/group in the container.
```
chown 1000:101 <bindconfig>/*
```

## Bind Logfiles

To log the bind logs on your docker host, just mount a directory into the docker container:
```
-v <logdir>:/var/log/named
```

Please verify, that the owner of the files is set equal to the uid/gid of the named user/group in the container.
```
chown 1000:101 <bindconfig>/*
```

## Bind Sample Configuration

A working bind configuration is provided in the [git repo](https://github.com/canarysat/alpine-dns/tree/master/bindconfig).
Just mount this example folder into the docker container and you're bind should work.

## Bind Test

You can test the dns responses with `dig` or `nslookup` on the docker host.
```
dig webmail.example.com @localhost

nslookup webmail.example.com localhost
```

## License

This project is licensed under `MIT <http://opensource.org/licenses/MIT>`_.
