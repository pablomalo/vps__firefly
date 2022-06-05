# vps__firefly

A personal finance application. See https://github.com/firefly-iii/firefly-iii

This stack includes two applications:
* Firefly itself, documentation at https://docs.firefly-iii.org/firefly-iii
* The CSV importer for Firefly, a.k.a. Firefly Data Importer (FIDI), documentation at https://docs.firefly-iii.org/data-importer/

This repo is intended to work in conjunction with https://github.com/pablomalo/vps

## Installation

> Prerequisite: install Traefik using https://github.com/pablomalo/vps

1. Init the submodule from within the parent repo.

2. To set environment variables for Firefly, copy `.env.dist` to `.env` and fill out. Pay particular attention to variables commented with the mention "TODO Set this". Other variables are optional (or already correctly set).

3. To set environment variables for FIDI, do the same with `fidi/env.dist`, paying special attention to `HOSTNAME` and `FIREFLY_III_URL`. The latter should match the value of `HOSTNAME` entered in step 2 above. The former should be a hostname specific to FIDI.

   NORDIGEN_ID` and `NORDIGEN_KEY` are not necessary if you intent to use FIDI only as a CSV importer (i.e. not importing data throught Nordigen's API).

   At this stage, leave `FIREFLY_III_ACCESS_TOKEN` blank.

4. `docker-compose up -d`

5. Complete the online setup process.

6. Online, in the app, head to the "profile" tab and create a Personal Access Token for Fidi. Take good note of it as it will be displayed only once.

7. Use this Personal Access Token to fill out the `FIREFLY_III_ACCESS_TOKEN` environment variable in `fidi/.env`.

8. Reload environment variables with `docker compose up -d`.

## How was AppServiceProvider altered?

For _FIDI (Firefly Data Importer)_ to function correctly, it needs to load its assets over HTTPS, not HTTP. Follow the steps below:

1. `docker compose up -d`
2. `docker compose exec it fidi bash`
3. `apt update`
4. `apt install vim`
5. `cd /var/www/html`
6. Edit `app/Providers/AppServiceProvider` and add the following lines to the `register()` method.
    ```php
    if (config('app.env') === 'production') { 
        $this->app['request']->server->set('HTTPS', true); 
    }
    ```