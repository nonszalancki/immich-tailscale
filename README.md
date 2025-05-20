# Immich server setup for docker compose
In this repo you'll find docker compose file for setting up [Immich](http://immich.app) server with Tailscale and Caddy for remote access.

It's aim is to setup Immich without machine learning so you can use it on some weaker hardware such as thin client or old pc. 

If you want to use machine learning funcionality while using simple hardware for your server, you can setup a [remote machine learning](#setup-remote-machine-learning) container on a separate machine.

I assume you already have docker installed on your machine and know the basics. If not, you can try some of the [Docker Tutorials](https://docs.docker.com/get-started/introduction/) and come back here later.

If you're a total begginer, it's recommended to start with learning about [Immich](http://immich.app) and at least check [requirements](https://immich.app/docs/install/requirements) and their instruction [how to run immich using docker compose](https://immich.app/docs/install/docker-compose).

## Setup Tailscale

In order to setup remote acces with tailscale you need to create your tailnet first:

1. Got to [Tailscale](http://tailscale.com) and create your account.
2. [Download and install](https://tailscale.com/kb/1347/installation) tailscale on you machines.
3. Go to your admin console, navigate to [Access controls](https://login.tailscale.com/admin/acls/file) and [define a tag that will identify your machine. For example you can use tag:immich by adding:

```bash
	"tagOwners": {
		"tag:immich": [
			"your@email.com",
		],
	},
```
By default Tailscale will allow all connections between machines in your tailnet. 
Later on you can define some Access Control Lists (ACLs) here for you machines.

4. Go to DNS settings and enable HTTPS Certificates and MagicDNS.

5. Create your Oauth key in [Settings](https://login.tailscale.com/admin/settings/oauth) and copy it to `tailscale` section in `docker-compose.yml`

## Setup Docker and Caddy

1. Fill `.env` at `UPLOAD_LOCATION=` and `DB_PASSWORD=`with your data.
2. Fill `Caddyfile` with your `machine name` and `tailnet`.
3. In `docker-compose.yml` under `enviroment:` in `tailscale` section:
	- Set your tag: `TS_EXTRA_ARGS: --advertise-tags=tag:your_tag`, by default it will be set to `immich`. 
	- Make sure your ` TS_AUTHKEY:` is set to OAuth key you've generated.

## Running server and getting TLS Certificates

1. Open your terminal and navigate to `immich-tailscale`
2. Run `docker compose up -d`, depending on your machine hardware and interned connection speed this can take a few minutes.
3. Once done, navigate to your [tailscale admin console](https://login.tailscale.com/admin/machines) and see if your machine is online. Try reaching your immich at `http://localhost:2283` to confirm it's up. 
4. In your terminal run `docker exec immich_tailscale tailscale --socket /tmp/tailscaled.sock cert <machine-name>.<tailnet-name>.ts.net` to obtain [your cert](https://tailscale.com/kb/1010/node-keys).
5. Remake your containers with `docker compose up -d`
6. Access your server at `https://<your-machine>.<your-tailnet>.ts.net`

## Setup remote machine learning

Setting up remote machine learning depends on a hardware. Navigate to the `immich_remote_ml` folder on your pc and fill the `compose.yaml` and `hwaccel.ml.yml` using [instructions on the immich site](https://immich.app/docs/guides/remote-machine-learning).
