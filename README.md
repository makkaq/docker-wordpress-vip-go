# WordPress VIP development for Docker

This repo provides a Docker-based development environment for [WordPress VIP Go][vip-go]
development. Via [chriszarate/wordpress][image], it runs on PHP7 and adds
WP-CLI, PHPUnit, Xdebug, and the WordPress unit testing suite. It further adds
VIP shared plugins, VIP mu-plugins, a [Photon][photon] server, and the
development plugins provided by VIP Quickstart.

If you only need Docker WordPress development environment for a single plugin or
theme, my [docker-compose-wordpress][simple] repo is a simpler place to start.


# "Classic" VIP

For an environment suitable for "classic" VIP development, check out my
[docker-wordpress-vip][vip] repo.


## Set up

1. Add `project.test` (or your chosen TLD) to your `/etc/hosts` file:

   ```
   127.0.0.1 localhost project.test
   ```

   If you choose a different TLD, edit `.env` as well.

2. Edit `setup.sh` to check out your organization’s code into the `src` folder
   folder (replace the example repo `Automattic/vip-go-skeleton` with your own).
   Then, adjust `docker-compose.yml` to reflect your changes (replace `vip-go-skeleton`
   and update `WORDPRESS_ACTIVATE_THEME` to reflect your theme).

3. Run `./setup.sh`.

4. Run `docker-compose up -d`.


## Interacting with containers

**Refer to [docker-compose-wordpress][simple] for general instructions** on how
to interact with the stack, including WP-CLI, PHPUnit, Xdebug, and preloading
content.

The main difference with this stack is that all code is synced to the WordPress
container from the `src` subfolder and, generally, is assumed to be its own
separate repo.


## Configuration

Put project-specific WordPress config in `conf/wp-local-config.php` and PHP ini
changes in `conf/php-local.ini`, which are synced to the container. PHP ini
changes are only reflected when the container restarts. You may also adjust the
Nginx config of the reverse proxy container via `conf/nginx-proxy.conf`.


## Photon

A [Photon][photon] server is included and enabled by default to more closely
mimic the WordPress VIP production environment. Requests to `/wp-content/uploads`
will be proxied to the Photon container—simply append Photon-compatible query
string parameters to the URL.


## HTTPS support

This repo provide HTTPS support out of the box. The setup script generates
self-signed certificates for the domain specified in `.env`. You may wish to add
the generated root certificate to your system’s trusted root certificates. This
will allow you to browse your dev environment over HTTPS without accepting a
browser security warning. On OS X:

```sh
sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain certs/ca-root/ca.crt
```

If you do not want to use HTTPS, add `HTTPS_METHOD: "nohttps"` to the
`services/proxy/environment` section of `docker-compose.yml`.


## Multiple environments

Multiple instances of this dev environment are possible. Make an additional copy
of this repo with a different folder name. Then, either juggle them by stopping
one and starting another, or modify `/etc/hosts` and `.env` to use another
domain, e.g., `project2.dev`.


[vip-go]: https://vip.wordpress.com/documentation/vip-go/
[photon]: https://jetpack.com/support/photon/
[image]: https://hub.docker.com/r/chriszarate/wordpress/
[simple]: https://github.com/chriszarate/docker-compose-wordpress
[vip]: https://github.com/chriszarate/docker-wordpress-vip
