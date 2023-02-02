easy NGINX + certbot docker setup

This example uses:
* [certbot docker image](https://github.com/coreycothrum/certbot_docker)
* [NGINX docker image](https://github.com/coreycothrum/nginx_docker)

## initial setup
1. `DOMAIN_NAME` has a default in [`.env`](.env). It should probably be something different (i.e. the actual domain name).
   This should be a permanent change, so modify/commit your local [`.env`](.env), or other ensure this is consistent from here on out.

2. generate self-signed seed certificate(s):

        docker compose down
        docker compose run --rm --entrypoint generate_self_signed_certs.sh certbot

3. start docker compose to let certbot do its thing:

        docker compose up    \
            --detach         \
            --force-recreate \
            --remove-orphans \
            --wait

   monitor to see if certbot succeeded:

        docker logs -f

4. reload NGINX to use the new cert(s):

        docker compose exec nginx nginx -s reload

5. Test https in browser (i.e. `https://$DOMAIN_NAME`). You should see the default NGINX page. If not using localhost, you should not see `ERR_CERT_AUTHORITY_INVALID` type warning(s).

## further configuration
after initial setup, you may want to further customize the configuration

### replace the static content
to serve new static content, simple mount a local directory over `/usr/share/nginx/html/`.

an example of this is commented out in [`docker-compose.yml`](docker-compose.yml).

### replace the `*.conf`
otherwise, replace the nginx conf file(s) as you normally would. [Use this as a starting point](https://github.com/coreycothrum/nginx_docker/blob/main/conf/nginx.conf).

this will likely require additional volume/mounts and/or environment variables to be added to [`docker-compose.yml`](docker-compose.yml) and/or [`.env`](.env).

## environment variables
| variable name          | default value         | description                 |
| ---------------------- | --------------------- | --------------------------- |
| `DOMAIN_NAME`          | `localhost.localhost` | domain name of server       |
