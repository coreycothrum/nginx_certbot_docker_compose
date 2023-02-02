easy NGINX + certbot docker compose app

this is a simple example integration of these docker images:
* [coreycothrum/certbot](https://github.com/coreycothrum/certbot_docker) ([on docker hub here](https://hub.docker.com/repository/docker/coreycothrum/certbot))
* [coreycothrum/nginx](https://github.com/coreycothrum/nginx_docker) ([on docker hub here](https://hub.docker.com/repository/docker/coreycothrum/nginx))

see [coreycothrum/fastapi_template](https://github.com/coreycothrum/fastapi_template) for an additional, more advanced, integration example.

## initial setup
1. `DOMAIN_NAME` has a default in [`.env`](.env). It should probably be something different (i.e. the actual domain name).
   This should be a permanent change, so modify/commit your local [`.env`](.env), or otherwise ensure this is consistent from here on out.

2. likewise `DOMAIN_EMAIL` should also be modified to your actual email. This is optional, but certbot will deliver security warnings/notifications here.

3. generate self-signed seed certificate(s):

        docker compose down
        docker compose run --rm --entrypoint generate_self_signed_certs.sh certbot

4. start docker compose:

        docker compose up    \
            --detach         \
            --force-recreate \
            --remove-orphans \
            --wait

5. use certbot to obtain legit certificates from [letsencrypt.com](letsencrypt.com):

        docker compose exec certbot request_certs.sh

   monitor to see if certbot succeeded:

        docker compose logs -f

6. reload NGINX to use the new cert(s):

        docker compose exec nginx nginx -s reload

7. Test https in browser (i.e. navigate to `https://$DOMAIN_NAME`). If not using `localhost`, you should not see `ERR_CERT_AUTHORITY_INVALID` type warning(s).

## environment variables
| variable name           | default value         | description                             |
| ----------------------- | --------------------- | --------------------------------------- |
| `DOMAIN_NAME`           | `localhost.localhost` | domain name of server                   |
| `DOMAIN_EMAIL`          |                       | email for cerbot security notifications |

## custom configuration options
after initial setup, you may want to further customize the NGINX configuration. See [coreycothrum/nginx_docker](https://github.com/coreycothrum/nginx_docker#custom-configuration-options) for available options.
