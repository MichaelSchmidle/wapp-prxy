# About wapp-prxy

This repository contains a reverse proxy wapp (short for "web app") based on [Nginx Proxy Manager](https://nginxproxymanager.com/). It's intended to be used in combination with [wappster](https://github.com/MichaelSchmidle/wappster/), a Docker-based tool to easily deploy and manage self-hosted wapps.

This wapp is a stand-alone extension of the [wapps](https://github.com/MichaelSchmidle/wapps/) catalogue. This means that it can be used both with and without other wapps from the catalogue.

# Why Extending the Wapps Catalogue With a Stand-Alone Wapp?

The prxy wapp is a bit of a special case. Wappster already comes with a built-in reverse proxy that automatically proxies and SSL-encrypts traffic to and from all wapps: [Træfik](https://traefik.io/traefik/). However, it's a hassle to put other web apps outside of your wappster instance behind Træfik (using [Træfik's file provider](https://doc.traefik.io/traefik/providers/file/)).

In comes this wapp: With its web-based management interface, it offers a convenient way of manually configuring proxies and SSL encryption for all sorts of endpoints *outside of your wappster instance*. The management interface of Nginx Proxy Manager itself is again proxied and SSL-encrypted by wappster. (Is your head spinning already? 😄)

To avoid the confusion about the two proxies and their different approaches, this wapp has been put in its own repository for the geeks among us.

# Requirements

## Step 1: Configure an Additional IP on Your Wappster Host

Make sure that your wappster host is reachable via two IPs.

Both wappster and this wapp need to listen to the same ports: http (80), https (443), and management port (default: 4443, depending on your wappster configuration). This is only possible if both wappster and this wapp listen to the same ports *on different IPs*.

Having two IPs allows for the following:

* Wappster listens on ``IP1:80``, ``IP1:443``, and ``IP1:4443`` (i.e. ``192.168.192.168:80`` and so on...) to proxy the management interface of wappster and wapp-prxy (as well as potentially all other wapps)
* Wapp-prxy listens on ``IP2:80``, ``IP2:443``, and ``IP2:4443`` (i.e. ``192.168.192.169:80`` and so on...) to proxy endpoints outside of your wappster instance

## Step 2: Set Up Wappster

In case you are setting up a fresh instance of wappster: To use wapp-prxy, set up your wappster instance first. Use the first IP while configuring wappster. Refer to [wappster's README](https://github.com/MichaelSchmidle/wappster/blob/master/README.md) for instructions on how to set up wappster.

In case you are using an existing instance of wappster: Make sure that the first IP is configured as wappster's IP in your wappster's ``.env`` file. If needed, change the IP setting in the ``.env`` file, then run the command ``docker-compose up -d`` again to restart wappster.

## Step 3: Create Stack

In your wappster management interface (i.e. Portainer), create a new stack.

As "Build Method", select "Repository" and add this repository's URL ``https://github.com/MichaelSchmidle/wapp-prxy/``.

Then, add the following environment variables by clicking the "Add Environment Variable" button accordingly:

| Name                | Value                                                                   |
|---------------------|-------------------------------------------------------------------------|
| HOST                | The DNS hostname pointing to this wapp (resolving to wappster's IP)     |
| IP                  | The dedicated IP of this wapp used to listen on ports 80, 443, and 4443 |
| MYSQL_DATABASE      | The name of the database                                                |
| MYSQL_ROOT_PASSWORD | The password of the database root user                                  |
| MYSQL_USER          | The name of the database user                                           |
| MYSQL_PASSWORD      | The password of the database user                                       |

Finally, hit the "Deploy the Stack" button in Portainer.

## Step 4: Log Into Wapp-Prxy

Log into your wapp-prxy with the [default admin user](https://nginxproxymanager.com/guide/#quick-setup). As the time of writing, the credentials are:

* Email: ``admin@example.com``
* Password: ``changeme``
