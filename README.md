easy to use [NGINX](https://www.nginx.com/) + [certbot](https://certbot.eff.org/) docker [compose](https://docs.docker.com/compose/) application

this is a simple example integration of these [docker](https://docs.docker.com/) images:
* [coreycothrum/certbot](https://github.com/coreycothrum/certbot_docker) ([on docker hub here](https://hub.docker.com/repository/docker/coreycothrum/certbot))
* [coreycothrum/nginx](https://github.com/coreycothrum/nginx_docker) ([on docker hub here](https://hub.docker.com/repository/docker/coreycothrum/nginx))

see [coreycothrum/fastapi_template](https://github.com/coreycothrum/fastapi_template) for an additional, more advanced, integration example.

## initial setup
1. Update [environment variables](#environment-variables). Defaults are in [`.env`](.env), but should be unique to your deployment.

   This should be a permanent change, so modify/commit your local [`.env`](.env), or otherwise set to ensure consistency from here on out.

2. generate self-signed seed certificate(s):

        docker compose down -v
        docker compose run  --rm --entrypoint generate_self_signed_certs.sh certbot

3. start docker compose:

        docker compose up    \
            --build          \
            --detach         \
            --force-recreate \
            --remove-orphans \
            --wait

4. use certbot to obtain legit certificates from [letsencrypt.com](letsencrypt.com):

        docker compose exec certbot request_certs.sh

   monitor to see if certbot succeeded:

        docker compose logs -f

5. reload NGINX to use the new cert(s):

        docker compose exec nginx nginx -s reload

6. Test in browser (i.e. navigate to `https://$DOMAIN_NAME`). If not using `localhost`, you should not see `ERR_CERT_AUTHORITY_INVALID` type warning(s).

## environment variables
| variable name           | default value         | description                                          |
| ----------------------- | --------------------- | ---------------------------------------------------- |
| `DOMAIN_NAME`           | `localhost.localhost` | domain name of server. default is OK for development |
| `DOMAIN_EMAIL`          |                       | email for cerbot security notifications              |

## custom configuration options
after initial setup, you may want to further customize the NGINX configuration. See [coreycothrum/nginx_docker](https://github.com/coreycothrum/nginx_docker#custom-configuration-options) for available options.
