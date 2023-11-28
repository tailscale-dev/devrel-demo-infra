# tailscale-dev/devrel-demo-infra

This repo contains Ansible code used to configure the backend of demos used by Dev Rel at events, etc.

## How to use

Firstly, this repo probably isn't really designed to operated outside of the devrel team but here's some documentation around it anyways. It assumes you're deploying the [id-headers-demo](https://github.com/pango-lin-tailnet/id-headers-demo) but you can deploy anything else you'd like using docker using this method too.

> You will need [casey/just](https://github.com/casey/just) as this runs wrappers around ansible commands for you.

In `groups_vars/demobox.yaml` define the containers you want to run. For example:

```
containers:
  - service_name: id-demo-test
    active: true
    image: ghcr.io/tailscale-dev/devrel-demo-infra:test
    volumes:
      - "{{ appdata_path }}/id-demo-test:/var/lib/tailscale"
    environment:
      - "TAILSCALE_AUTHKEY={{ tsauthkey_id_demo_test }}"
      - TAILSCALE_HOSTNAME=id-demo-docker
      - TAILSCALE_FUNNEL=on
    include_global_env_vars: true
    restart: unless-stopped
```

+ `image: ghcr.io/tailscale-dev/devrel-demo-infra:test` = This can be any docker image, but here we assume each demo has something unique so push your unique image to a unique tag.
+ `TAILSCALE_AUTHKEY` = The authkey is encrypted in `vars/vault.yaml`. To add a new key run `just vault decrypt`, edit `vault.yaml` and then `just vault encrypt`.
+ `TAILSCALE_HOSTNAME` = This is the hostname that funnel/serve will use. E.g. your demo will be at `id-demo-docker.pango-lin.ts.net`.
+ `TAILSCALE_FUNNEL` = Turns funnel on or off.

Once happy with your edits, run the command `just compose demobox`. Under the hood, Ansible will run and ingest your configuration spitting out a `docker-compose.yaml` file in the home dir of the ssh user. 

```
# run on your local dev machine
## configure the whole node
just run demobox

## update just the docker-compose file
just compose demobox
```

Once the automation is run it is up to the user to manually start the new containers on the remote host `dcp pull` followed by `dcp up -d` (`dcp` is an alias for `docker-compose`).

```
# run on the remote node demobox
## pulls the latest images
dcp pull

## starts the containers
dcp up -d
```

# Resources 
- https://tailscale.dev/blog/docker-mod-tailscale