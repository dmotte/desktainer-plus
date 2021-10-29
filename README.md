# desktainer-plus

![](desktainer-plus-icon-128.png)

[![GitHub Workflow Status](https://img.shields.io/github/workflow/status/dmotte/desktainer-plus/docker?logo=github&style=flat-square)](https://github.com/dmotte/desktainer-plus/actions)
[![Docker Pulls](https://img.shields.io/docker/pulls/dmotte/desktainer-plus?logo=docker&style=flat-square)](https://hub.docker.com/r/dmotte/desktainer-plus)

:computer: Remote **desk**top in a cont**ainer** (extended version). This is an extension of the [dmotte/desktainer](https://github.com/dmotte/desktainer) Docker image.

> :package: This image is also on **Docker Hub** as [`dmotte/desktainer-plus`](https://hub.docker.com/r/dmotte/desktainer-plus) and runs on **several architectures** (e.g. amd64, arm64, ...). To see the full list of supported platforms, please refer to the `.github/workflows/docker.yml` file. If you need an architecture which is currently unsupported, feel free to open an issue.

> :calendar: The build process of this Docker image is **triggered automatically every month** (thanks, [GitHub Actions](https://github.com/features/actions)! :smile:) to ensure that you get it with all the latest updated packages. See the [workflow file](.github/workflows/docker.yml) for further information.

## Extensions

On top of the base [dmotte/desktainer](https://github.com/dmotte/desktainer) image, we have:

- installed some **additional packages** (`nano`, `curl`, `zip`, `tmux`, etc.)
- installed the **Firefox** web browser
- installed the **OpenSSH server**
  - configured it in *supervisor* as a service
  - running on **port 22**
- installed **Shell In A Box**
  - configured it in *supervisor* as a service
  - running on **port 4200**
- already created a custom user named `debian` and made some customizations to it

See the `docker-build/Dockerfile` file for further details.

## Usage

The first thing you'll need are **host keys** for the OpenSSH server. You can generate them with the following commands:

```bash
mkdir -p hostkeys/etc/ssh
ssh-keygen -A -f hostkeys
mv hostkeys/etc/ssh/* hostkeys
rm -r hostkeys/etc
```

If you omit this step, the **OpenSSH server** will actively **refuse all the connections** (which is OK if you don't need it).

Then you can start your container with:

```bash
docker run -it --rm \
    -v $PWD/hostkeys/ssh_host_dsa_key:/etc/ssh/ssh_host_dsa_key:ro \
    -v $PWD/hostkeys/ssh_host_dsa_key.pub:/etc/ssh/ssh_host_dsa_key.pub:ro \
    -v $PWD/hostkeys/ssh_host_ecdsa_key:/etc/ssh/ssh_host_ecdsa_key:ro \
    -v $PWD/hostkeys/ssh_host_ecdsa_key.pub:/etc/ssh/ssh_host_ecdsa_key.pub:ro \
    -v $PWD/hostkeys/ssh_host_ed25519_key:/etc/ssh/ssh_host_ed25519_key:ro \
    -v $PWD/hostkeys/ssh_host_ed25519_key.pub:/etc/ssh/ssh_host_ed25519_key.pub:ro \
    -v $PWD/hostkeys/ssh_host_rsa_key:/etc/ssh/ssh_host_rsa_key:ro \
    -v $PWD/hostkeys/ssh_host_rsa_key.pub:/etc/ssh/ssh_host_rsa_key.pub:ro \
    -p 6901:6901 \
    -p 2222:22 \
    -p 4200:4200 \
    dmotte/desktainer-plus
```

Then:

- head over to http://localhost:6901/ to access the **remote desktop**;
- head over to http://localhost:4200/ to access the in-browser **remote shell**;
- connect to `localhost` on port `2222` via *SSH* to log into the **OpenSSH server**.

![screen01](screen01.png)

For a more complex usage example, refer to the `docker-compose.yml` file.

> :bulb: **Tip**: If you need to, you can further extend this project by making your own `Dockerfile` starting from this image (i.e. `FROM dmotte/desktainer-plus`) and/or mount custom *supervisor* configuration files.

### Environment variables

Same as the [dmotte/desktainer](https://github.com/dmotte/desktainer) project.

## Development

If you want to contribute to this project, the first thing you have to do is to **clone this repository** on your local machine:

```bash
git clone https://github.com/dmotte/desktainer-plus.git
```

Then you'll have to create your **host keys** (see the [Usage](#Usage) section of this document) inside the `vols-desktainer-plus` directory and run:

```bash
docker-compose down && docker-compose up --build
```

This will automatically **build the Docker image** using the `docker-build` directory as build context and then the **Docker-Compose stack** will be started.

If you prefer to run the stack in daemon (detached) mode:

```bash
docker-compose up -d
```

In this case, you can view the logs using the `docker-compose logs` command:

```bash
docker-compose logs -ft
```
