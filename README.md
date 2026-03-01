# Homeground

This repository instructs you on setting up a reverse proxy using NGINX to allow you to access your rtsp enabled cameras behind your router at home from anywhere and in this particular scenario display the video stream on a Grafana dashboard.
I am using a Raspberry Pi 5 however this should work on any other Linux computer as well.


## Prerequisits

- Up to date Linux computer such as a Raspberry Pi 5
- Webcams supporting RTSP connected to your local network
- Network router supporint port forwarding


## Preparation

1. [Install Docker](https://docs.docker.com/engine/install/debian/#install-using-the-repository) on your Linux computer
2. Install Docker Compose `sudo apt install docker-compose`
3. Clone this repository onto the device


## Network Configuration

To be able to reach your reverse proxy we are going to setup later, you need to open two network ports:
- Port 80 (Needed to create and update TLS certificates provided by [Let's Encrypt](https://letsencrypt.org/getting-started/))
- Port 443 (Used for all the other traffic)

Port forwarding can cause security issues if not done correctly! If you are unfamiliar please talk to somebody who is familiar with the topic and/familiarize yourself [Port Forwarding a Basic Guide](https://www.youtube.com/watch?v=6ExS1oRhhYQ).

As most people won't have static public IP addresses / domains, I recommend to consider dynamic DNS (DDNS) providers such as DuckDNS or DynDNS. In my case my internet provider provides this as a free service. 

- Once you have your domain, you have to update the `nginx.conf` file and replace all occurences of `yourdomain.com` with your domain name.

Verify your setup as follows:

1. Start the NGINX container: `sudo docker-compose up -d nginx`
2. Access `http://yourdomain.com/.well-known/acme-challenge/test.txt` and check if yout get a re


## Retrieve TLS Certificates

To encrypt the traffic between a browser outside of your local network and the reverse proxy behind your router, we need to create TLS certificates. An easy way to do this is via [Let's Encrypt](https://letsencrypt.org/docs/) and [Certbot](https://certbot.eff.org/).

The following command will run the Certbot container and generate a certificate for your domain.

Update the `email address` and `yourdomain.com` accordingly before running the command on your Linux computer:

```shell
sudo docker-compose run --rm certbot certonly --webroot --webroot-path=/var/www/certbot \
  --email your@email.com --agree-tos --no-eff-email \
  --disable-hook-validation \
  -d yourdomain.com
```

You can check if the certifactes area available with the following command:

```shell
sudo ls ./certbot/conf/live/yourdomain.com/
```

## Activate TLS Certificates

Now that the certificates have been generated and downloaded, you can activate the TLS configuration in `nginx.conf` by changing and uncommenting the following lines:

- Line 21: Change to `listen 443 ssl;`
- Line 24 and 25: Uncomment

Then restart the all containers with:

```shell
sudo docker-compose down
sudo docker-compose up -d
```


## Add Authentication

Create .htpassword file

```shell
docker run --rm --entrypoint htpasswd httpd -Bbn admin password > .htpasswd
```
