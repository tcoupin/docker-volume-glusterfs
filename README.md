# Docker volume plugin for GlusterFS

This plugin uses GlusterFS as distributed data storage for containers.

[![Release](https://img.shields.io/github/release/amarkwalder/docker-volume-glusterfs.svg)](https://github.com/amarkwalder/docker-volume-glusterfs/releases/latest)
[![TravisCI](https://travis-ci.org/amarkwalder/docker-volume-glusterfs.svg)](https://travis-ci.org/amarkwalder/docker-volume-glusterfs)

## Installation

Using go (until we get proper binaries):

```
$ go get github.com/amarkwalder/docker-volume-glusterfs
```

## Usage

This plugin doesn't create volumes in your GlusterFS cluster yet, so you'll have to create them yourself first.

1 - Start the plugin using this command:

```
$ sudo docker-volume-glusterfs -servers gfs-1:gfs-2:gfs-3
```

We use the flag `-servers` to specify where to find the GlusterFS servers. The server names are separated by colon.

2 - Start your docker containers with the option `--volume-driver=glusterfs` and use the first part of `--volume` to specify the remote volume that you want to connect to:

```
$ sudo docker run --volume-driver glusterfs --volume datastore:/data alpine touch /data/helo
```

See this video for a slightly longer usage explanation:

https://youtu.be/SVtsT9WVujs

## Driver options 

- replica: replica for glusterfs (default to length of peers)

### Volume creation on demand

This extension can create volumes on the remote cluster if you install https://github.com/aravindavk/glusterfs-rest in one of the nodes of the cluster.

You need to set two extra flags when you start the extension if you want to let containers to create their volumes on demand:

- rest: is the URL address to the remote api.
- gfs-base: is the base path where the volumes will be created.

This is an example of the command line to start the plugin:

```
$ docker-volume-glusterfs -servers gfs-1:gfs2 \
    -rest http://gfs-1:9000 -gfs-base /var/lib/gluster/volumes
```

These volumes are replicated among all the peers in the cluster that you specify in the `-servers` flag.

### Starting on boot

For Systemd based distros, use the files in `contrib/systemd`.

```
mv docker-volume-glusterfs /usr/local/bin/
mv contrib/systemd/etc/systemd/system/docker-volume-glusterfs.service /etc/systemd/system/
mv contrib/systemd/etc/sysconfig/docker-volume-glusterfs.conf /etc/sysconfig/
systemctl daemon-reload
```

Edit the file /etc/sysconfig/docker-volume-glusterfs.conf` with your config

```
systemctl start docker-volume-glusterfs && systemctl enable docker-volume-glusterfs
```

## LICENSE

MIT
