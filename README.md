# tinbox-docker
Docker orchestration for tinbox.

## Overview
A work-in-progress overview of tinbox services and docker containers.

```
                             ╭───────────────╮
                             │     nginx     │
                             ╞═══════════════╡
                             │      app      ├───────────┐
                             ╰───────┬───────╯           │
                                     │                   │
         ╭───────────────╮   ╭───────┴───────╮   ╭───────┴───────╮
(IMAP)   │      mx       ├───┤      api      │   │     socker    │
         ╰───────────────╯   ╰──┬─────────┬──╯   ╰────────────┬──╯
                                │         │                   │
                   ╭────────────┴──╮   ╭──┴────────────╮   ╭──┴────────────╮   ╭───────────────╮
                   │    postgres   │   │ elasticsearch │   │     redis     │   │   rabbitmq    │
                   ╰───────────────╯   ╰───────────────╯   ╰───────────────╯   ╰───────────────╯

         ╭───────────────╮
         │    www_data   │
         ╰───────────────╯
```

## Setup

Build tinbox docker image:
``` sh
cd <tinbox repo>
# --force-rm not required but handy if something goes wrong
docker build --force-rm -t tinbox_api .
```

Build tinbox-mx docker image:
``` sh
cd <tinbox-mx repo>
docker build --force-rm -t tinbox_mx .
```

Build tinbox-app docker image:
``` sh
cd <tinbox-app repo>
docker build --force-rm -t tinbox_app .
```

Build remaining images (nginx, db, etc.):
``` sh
cd <tinbox-docker repo>/tinbox
docker-compose build
```

Migrate database and collect static assets
``` sh
docker-compose run --rm api migrate --noinput
docker-compose run --rm api collectstatic --noinput
```

## Run

Start everything:
``` sh
docker-compose up -d
```

## Develop

For most cases an image can just be rebuild via compose.

``` sh
docker-compose up -d api
```

This will rebuild/restart the api service along with dependent services, if needed.
Pass `--force-recreate` to force the image to be re-created.


### Misc handy commands

Rebuild (only) nginx container to get new app container data:
``` sh
docker-compose up --force-recreate --no-deps -d nginx
```

Remove dangling images:
``` sh
docker rmi $(docker images --quiet --filter "dangling=true")
```
