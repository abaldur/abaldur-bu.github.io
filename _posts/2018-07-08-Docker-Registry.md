---
title: Setting up a Docker Registry for LAN
published: true
categories: docker
---

This is a guide to setting up a *docker registry* that can be used on a LAN.

 where
 several machines would like to have a docker registry from where docker images can be shared between different users.

Most of what is written here can be found in the official docker references listed [here](#References).

# [](#header-1)Using self-signed certificate

Assuming you would like to h

* $HOSTNAME = `myhost`

## [](#header-2)Configuring the machine hosting the docker registry

The first step is to setup the ...

On the machine that should run the docker registry, create a folder to hold the generated certificates:

```
mkdir -p ~/docker_ws/registry_settings/certs && cd ~/docker_ws/registry_settings
```

Generate your own (self-signed) certificate using the `openssl` tool:

```
openssl req \
  -newkey rsa:4096 -nodes -sha256 -keyout certs/domain.key \
  -x509 -days 365 -out certs/domain.crt
```

When you are asked to enter *Common Name* type in `myhost.local`. Running `ls certs` should output two files:

```
ls certs
domain.crt domain.key
```

Now the *docker registry* can be started with TLS by running the commands:

```
docker run -d \
  --restart=always \
  --name registry \
  -v ~/docker_ws/registry_settings/certs:/certs \
  -v myhost_registry:/var/lib/registry \
  -e REGISTRY_HTTP_ADDR=0.0.0.0:443 \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt \
  -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key \
  -p 443:443 \
  registry:2
```

## [](#header-2)Configuring clients of the docker registry


Instruct every Docker daemon to trust that certificate. If `myhost` also would like to push/pull images from the registry, it should perform the following steps as well:

```
$ sudo mkdir -p /etc/docker/certs.d/myhost.local:441
$ sudo cp ~/docker_ws/registry/certs/domain.crt /etc/docker/certs.d/myhost.local:443/ca.crt
```

> The `/etc/docker/certs.d` folder does not exist by default.
> The file `domain.crt` is renamed to `ca.crt` when moved to `certs.d/myhost.local:443`.

# [](#header-1)References

* [1] <https://docs.docker.com/registry/insecure/>
* [2] <https://docs.docker.com/registry/deploying/#customize-the-storage-back-end>
