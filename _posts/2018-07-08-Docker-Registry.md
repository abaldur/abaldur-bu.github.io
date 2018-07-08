---
title: How to setup a Docker Registry
published: true
categories: docker
---

This is a guide to setting up a docker registry that can be used on a local network.

# [](#header-1)Using self-signed certificate

Assuming you would like to h

* $HOSTNAME = `myhost`

On the machine that should run the docker registry, create a folder to hold the generated certificates:

```
$ mkdir -p ~/docker_ws/registry/certs && cd ~/docker_ws/registry
```

Generate your own certificate using the `openssl` tool:

```
openssl req \
  -newkey rsa:4096 -nodes -sha256 -keyout certs/domain.key \
  -x509 -days 365 -out certs/domain.crt
```

When you are asked to enter "Common Name" type in `myhost.local`. Running `ls certs` should output two files:

```
$ ls certs
domain.crt domain.key
```

Instruct every Docker daemon to trust that certificate.

```
$ sudo mkdir /
```

# [](#header-1)References

* [1] https://docs.docker.com/registry/insecure/
* [2] https://docs.docker.com/registry/deploying/#customize-the-storage-back-end
