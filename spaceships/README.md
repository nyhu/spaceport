### Spaceship Section

Spaceships are independents and can be placed anywhere on spaceport's server regardless spaceport's path.

Taking a sample-website:

```
  sample-website:
    restart: always
    image: sample-website
    build: ./samples/website
    container_name: sample-website
    volumes:
      - "./conf.d/:/etc/nginx/conf.d"
    environment:
      - VIRTUAL_HOST=samplewebsite.example.com
      - VIRTUAL_NETWORK=nginx-proxy
      - VIRTUAL_PORT=80
      - LETSENCRYPT_HOST=sample.example.com
      - LETSENCRYPT_EMAIL=email@example.com
```
The important part here are the environment variables. These are used by the config generator and certificate maintainer containers to set up the system.

In a real-world scenario these images would likely come from a Docker registry, an opensource git project like **goland_consular_ship**, or deployed with a deployment key : [github deploy keys](https://developer.github.com/v3/guides/managing-deploy-keys/)

Each web framework and project can have a very different workflow. I added several examples to help you find inspiration for your own needs.

### Contribute !

Feel free to contribute with new spaceship pull request or improvements on given spaceships.
