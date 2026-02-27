# Homeground

https://hub.docker.com/r/alexxit/go2rtc

## Setup

```shell
docker pull alexxit/go2rtc
```

Basic Deployment

```shell
docker run -d \
  --name go2rtc \
  -p 1984:1984 \
  --restart unless-stopped \
  -v ~/go2rtc:/config \
  alexxit/go2rtc
```

Deployment with GPU Acceleration


```shell
docker run -d \
  --name go2rtc \
  --network host \
  --privileged \
  --restart unless-stopped \
  --gpus all \
  -v ~/go2rtc:/config \
  alexxit/go2rtc:latest-hardware
```

## Docker Compose Setup

Create .htpassword file

```shell
docker run --rm --entrypoint htpasswd httpd -Bbn admin password > .htpasswd
```

## Certbot generate certificates

```shell
docker-compose run --rm certbot certonly --webroot --webroot-path=/var/www/certbot \
  --email your@email.com --agree-tos --no-eff-email \
  -d yourdomain.com
```
