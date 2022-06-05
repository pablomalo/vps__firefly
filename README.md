# vps__firefly

A personal finance application. See https://github.com/firefly-iii/firefly-iii

This stack includes two applications:
* Firefly itself, documentation at https://docs.firefly-iii.org/firefly-iii
* The CSV importer for Firefly, a.k.a. Firefly Data Importer (FIDI), documentation at https://docs.firefly-iii.org/data-importer/

This repo is intended to work in conjunction with https://github.com/pablomalo/vps

## Installation

> Prerequisite: install Traefik using https://github.com/pablomalo/vps

1. Init the submodule from within the parent repo.

2. Set up the Firefly app

   1. cd inside `firefly/app`
   
   2. Copy `.env.dist` to `.env` and fill out. Pay particular attention to variables commented with the mention "TODO Set this". Other variables are optional (or already correctly set).
   
   3. Copy `.db.env.dist` to `.db.env` and fill out. The variables `MYSQL_DATABASE`, `MYSQL_USER` `MYSQL_PASSWORD` must match the variables `DB_DATABASE`, `DB_USERNAME` and `DB_PASSWORD` previously defined in `.env`.
   
   4. `docker-compose up -d`
   
   5. Complete the online setup process, e.g. create the root user.
   
   6. Under the "profile" tab, create a Personal Access Token for Fidi. Take good note of it as it will be displayed only once.
   
3. Set up FIDI

   1. cd inside `firefly/fidi`.
   
   2. Copy `.env.dist` to `.env`, paying special attention to `HOSTNAME` and `FIREFLY_III_URL`. `HOSTNAME` should be a hostname specific to FIDI. `FIREFLY_III_URL` should match the value of `HOSTNAME` entered when setting up the Firefly app (but including the "https://" prefix).

      NORDIGEN_ID` and `NORDIGEN_KEY` are not necessary if you intend to use FIDI only as a CSV importer (i.e. not importing data throught Nordigen's API).
      
      Use the Personal Access Token created above as the value for `FIREFLY_III_ACCESS_TOKEN`.
   3. `docker compose up -d`
   
   4. Online, in the FIDI app, you may need to press the "Reauthenticate" button.

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