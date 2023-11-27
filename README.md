# tailscale-dev/devrel-demo-infra

This repo contains Ansible code used to configure the backend of demos used by Dev Rel at events, etc.

## How to use

Firstly, this repo probably isn't really designed to operated outside of the devrel team but here's some documentation around it anyways.

> You will need [casey/just](https://github.com/casey/just) as this runs wrappers around ansible commands for you.

In `groups_vars/demobox.yaml` you define the containers you want to run. Then run the command `just compose demobox` and it will ingest your configuration and spit out a docker-compose.yaml file in the home dir of the ssh user. Once the automation is run it is up to the user to manually start the new containers on the remote host `dcp pull` followed by `dcp up -d` (`dcp` is an alias for `docker-compose`). 

```
## configure the whole node
just run demobox

## update just the docker-compose file
just compose demobox
```