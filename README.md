# changes / to do
1. clone the docker-concourse starter files, modify docker-compose configuration per tutorial:
- docker-concourse: https://github.com/concourse/concourse-docker
- tutorial: https://qiita.com/slave125/items/38a156d4f2efd891e6e5#concourse-ci%E3%81%AE%E7%92%B0%E5%A2%83%E6%A7%8B%E7%AF%89mac%E7%B7%A8

2. add .env configs
3. run generate-keys script per docker-concourse 
4. docker-compose up
5. install concourse for mac from releases https://github.com/concourse/concourse/releases (Do not use docker-based release here)
6. run start-darwin.sh (confirm concourse install is in right folder and same with working dir)
7. login to fly
8. make sure ci has 2 workers (linux and darwin) with `fly -t mytarget workers`
9. set pipeline to test config to make sure git reference works and running with darwin worker works.


# Concourse Docker

This Docker image simply packages up the official `concourse` binary and
configures it as the `ENTRYPOINT`, with a bunch of sane defaults for Docker.

Configuration is done via `CONCOURSE_*` environment variables. To discover
them, run `--help`:

```sh
docker run -t concourse/concourse --help
docker run -t concourse/concourse web --help
docker run -t concourse/concourse worker --help
```

See [the Concourse install docs](https://concourse-ci.org/install.html) for more
information on deploying and managing Concourse - the Docker repository just
wraps the `concourse` binary, so the documentation covers it too.

## Running with `docker-compose`

The `docker-compose.yml` in this repo will get you up and running with the
latest version Concourse. To use it you'll first need to execute
`./keys/generate` - this will generate credentials used to authorize the
Concourse components with each-other:

```sh
$ ./keys/generate
wrote private key to /keys/session_signing_key
wrote private key to /keys/tsa_host_key
wrote ssh public key to /keys/tsa_host_key.pub
wrote private key to /keys/worker_key
wrote ssh public key to /keys/worker_key.pub
```

Next, run `docker-compose up -d` to start Concourse in the background:

```sh
$ docker-compose up -d
Starting concourse-docker_db_1 ... done
Starting concourse-docker_web_1 ... done
Starting concourse-docker_worker_1 ... done
```

The default configuration sets up a `test` user with `test` as their password
and grants them access to `main` team. To use this in production you'll
definitely want to change that - see [Auth &
Teams](https://concourse-ci.org/auth.html) for more information..

If things seem to be going wrong, check the logs for any errors:

```sh
$ docker-compose logs -f
Attaching to concourse-docker_worker_1, concourse-docker_web_1, concourse-docker_db_1
...
```

## Running with `docker run`

Concourse components can also be run with regular old `docker run` commands.
Please use `docker-compose.yml` as the canonical reference for the necessary
flags/vars and connections between components. Further documentation on
configuring Concourse is available in the [Concourse Install
docs](https://concourse-ci.org/install.html).

## Building `concourse/concourse`

The `Dockerfile` in this repo is built as part of our CI process - as such, it
depends on having a pre-built `linux-rc` available in the working directory, and
ends up being published as `concourse/concourse`.
