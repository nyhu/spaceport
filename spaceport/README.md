# Spacioport Section

### How does it work

The system consists of 4 main parts:

* Main Nginx reverse proxy container (spaceport).
* Container that generates the main Nginx config based on container metadata.
* Container that automatically handles the acquisition and renewal of Let's Encrypt TLS certificates.
* The actual websites living in their own containers (spaceships).

### The main Nginx reverse proxy container

This is the only publicly exposed container, routes traffic to the backend servers and provides TLS termination.

Uses the official [nginx](https://hub.docker.com/_/nginx/) Docker image.

It is defined in `docker-compose.yml` under the **nginx** service block:

```
services:
  nginx:
    ...
```

As you can see it shares a few volumes:
* Configuration folder: used by the container that generates the configuration file.
* Default Nginx root folder: used by the Let's Encrypt container for challenges from the CA. 
* Certificates folder: written to by the Let's Encrypt container, this is where the TLS certificates are maintained. 

### The configuration generator container

This container inspects the other running containers and based on their metadata (like **VIRTUAL_HOST** environment variable) and a template file it generates the Nginx configuration file for the main Nginx container. When a new container is spinning up this container detects that, generates the appropriate configuration entries and restarts Nginx. 

Uses the [jwilder/docker-gen](https://hub.docker.com/r/jwilder/docker-gen/) Docker image.

It is defined in `docker-compose.yml` under the **nginx-gen** service block:

```
services:
  ...

  nginx-gen:
    restart: always
    ...
```

The container reads the `nginx.tmpl` template file (source: [jwilder/nginx-proxy](https://github.com/jwilder/nginx-proxy)) via a volume shared with the host.

It also mounts the Docker socket into the container in order to be able to inspect the other containers (the `"/var/run/docker.sock:/tmp/docker.sock:ro"` line). 
**Security warning**: mounting the Docker socket is usually discouraged because the container getting (even read-only) access to it can get root access to the host. In our case, this container is not exposed to the world so if you trust the code running inside it the risks are probably fairly low. But definitely something to take into account. See e.g. [The Dangers of Docker.sock](https://raesene.github.io/blog/2016/03/06/The-Dangers-Of-Docker.sock/) for further details.

NOTE: it would be preferrable to have docker-gen only handle containers with exposed ports (via `-only-exposed` flag in the `entrypoint` script above) but currently that does not work, see e.g. <https://github.com/jwilder/nginx-proxy/issues/438>.

### The Let's Encrypt container

This container also inspects the other containers and acquires Let's Encrypt TLS certificates based on the **LETSENCRYPT_HOST** and **LETSENCRYPT_EMAIL** environment variables. At regular intervals it checks and renews certificates as needed. 

Uses the [jrcs/letsencrypt-nginx-proxy-companion](https://hub.docker.com/r/jrcs/letsencrypt-nginx-proxy-companion/) Docker image.

It is defined in `docker-compose.yml` under the **letsencrypt-nginx-proxy-companion** service block:

```
services:
  ...

  letsencrypt-nginx-proxy-companion:
    restart: always
    ...
```

The container uses a volume shared with the host and the Nginx container to maintain the certificates.

It also mounts the Docker socket in order to inspect the other containers. See the security warning above in the docker-gen section about the risks during this process.

### Known issues

- Impatient developper rerun nginx-gen to make certificate and nginx.conf generation faster. It can be necessary if you have no second to loose.
- To modify templates/nginx.tmpl, You need to recreate it as the template modification is aknowledge by nginx-gen based on creation time instead of modification time.