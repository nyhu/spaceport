# Spaceport a Docker + Nginx + Let's Encrypt server setup

A complete configuration to run multiple websites on a single cloud instance.

This simple example shows how to set up multiple websites running behind a dockerized Nginx reverse proxy and served via HTTPS using free [Let's Encrypt](https://letsencrypt.org) certificates. New sites can be added on the fly by creating a new spaceship based on exemples and then running `docker-compose up` as the main Nginx config is automatically updated and certificates (if needed) are automatically acquired.

Some of the configuration are derived from <https://github.com/fatk/docker-letsencrypt-nginx-proxy-companion-examples> with some simplifications and updates to work with current `nginx.tmpl` from [nginx-proxy](https://github.com/jwilder/nginx-proxy) and docker-compose v2 files.

### Prerequisites

* [docker](https://docs.docker.com/engine/installation/) (>= 1.10)
* [docker-compose](https://github.com/docker/compose/releases) (>= 1.8.1)
* access to (sub)domain(s) pointing to a publicly accessible server (required for TLS)

### Preparation

* Clone the [repository](https://github.com/nyhu/spaceport) on the server pointed to by your domain. 
* In `docker-compose.yml` in ./spaceships/your_ship_type: 
  * Change the **VIRTUAL_HOST** and **LETSENCRYPT_HOST** to your domains.
  * Change **LETSENCRYPT_EMAIL** entries to the email address you want to be associated with the certificates. 

### Running

###### Step one: Spaceport

In the spaceport run: 
```
docker-compose up
```

This will perform the following steps:
* Download the required images from Docker Hub ([nginx](https://hub.docker.com/_/nginx/), [docker-gen](https://hub.docker.com/r/jwilder/docker-gen/), [docker-letsencrypt-nginx-proxy-companion](https://hub.docker.com/r/jrcs/letsencrypt-nginx-proxy-companion/)).
* Create containers from them.
* Start up the containers. 
  * *docker-letsencrypt-nginx-proxy-companion* inspects containers' metadata and tries to acquire certificates as needed (if successful then saving them in a volume shared with the host and the Nginx container).
  * *docker-gen* also inspects containers' metadata and generates the configuration file for the main Nginx reverse proxy

###### Step two: Spaceship(s)

In the spaceship you chose, update conf.d/<your-domain-name>.conf: 
Modifie domains to match your own.
Do the same in associated docker-compose.yml
You should also rename the service base on your project.

Then in spaceships/your_project run:
```
docker-compose up
```

After a bit, certificates will be created in spaceport/proxy/certs and updated automatically when needed.
You website is then accessible on https.

###### Optionnal: Gitlab

An optionnal Gitlab setup is given in docks. Feel free to lauch it if you need your personnal git server.
As for spaceship, you just have to replace user/passwords from some of your choosing and domain name to one pointing on your server. 

### Troubleshooting

* To view logs run `docker-compose logs`.
* To view the generated Nginx configuration run `docker exec -ti nginx cat /etc/nginx/conf.d/default.conf`

### Contribute !

Feel free to contribute with new spaceship pull request or improvements on given spaceships.

### Conclusion

This project was born at school 42 in Paris during a [matrice](https://matrice.io). We needed to share a given server between multiple growing start-up. After 6 month of contributions we decided to share some processes based on open source project.

This can be a fairly simple way to have easy, reproducible deploys for websites with free, auto-renewing TLS certificates. 
You can now share a server between friends and learn from each others.
