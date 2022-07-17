# desktainer-plus

![icon](icon-128.png)

[![GitHub Workflow Status](https://img.shields.io/github/workflow/status/dmotte/desktainer-plus/release?logo=github&style=flat-square)](https://github.com/dmotte/desktainer-plus/actions)
[![Docker Pulls](https://img.shields.io/docker/pulls/dmotte/desktainer-plus?logo=docker&style=flat-square)](https://hub.docker.com/r/dmotte/desktainer-plus)

:computer: Remote **desk**top in a cont**ainer** (extended version). This is an extension of the [dmotte/desktainer](https://github.com/dmotte/desktainer) Docker image.

> :package: This image is also on **Docker Hub** as [`dmotte/desktainer-plus`](https://hub.docker.com/r/dmotte/desktainer-plus) and runs on **several architectures** (e.g. amd64, arm64, ...). To see the full list of supported platforms, please refer to the [`.github/workflows/release.yml`](.github/workflows/release.yml) file. If you need an architecture which is currently unsupported, feel free to open an issue.

> :calendar: The build process of this Docker image is **triggered automatically every month** (thanks, [GitHub Actions](https://github.com/features/actions)! :smile:) to ensure that you get it with all the latest updated packages. See the [workflow file](.github/workflows/release.yml) for further information.

## Extensions

On top of the base [dmotte/desktainer](https://github.com/dmotte/desktainer) image, we have:

- installed some **additional packages** (`nano`, `curl`, `zip`, `tmux`, etc.)
- installed the **Firefox** web browser
- installed the **OpenSSH server**
  - configured it in _supervisor_ as a service
  - running on **port 22**
  - missing OpenSSH server **host keys** are **generated automatically** at container startup
- installed **Shell In A Box**
  - configured it in _supervisor_ as a service
  - running on **port 4200**
- already created a custom user named `mainuser` and made some customizations to it
- declared a persistent `/data` volume

See the [`build/Dockerfile`](build/Dockerfile) file for further details.

## Usage

The simplest way to try this image is:

```bash
docker run -it --rm -p 6901:6901 -p 2222:22 -p 4200:4200 dmotte/desktainer-plus
```

> :warning: This is **not recommended** for production though, since the missing OpenSSH server host keys are generated at container creation and so they are not persisted.

Then:

- head over to http://localhost:6901/ to access the **remote desktop**;
- head over to http://localhost:4200/ to access the in-browser **remote shell**;
- connect to `localhost` on port `2222` via _SSH_ to log into the **OpenSSH server**.

![Screenshot](screen-01.png)

## Standard usage

The [`docker-compose.yml`](docker-compose.yml) file contains a complete usage example for this image. Feel free to simplify it and adapt it to your needs. Unless you want to build the image from scratch, comment out the `build: build` line to use the pre-built one from _Docker Hub_ instead.

> :information_source: If you want to use **persistent host keys** for the OpenSSH server (recommended for production) you can generate them with the following commands before running the container:
>
> ```bash
> mkdir -p hostkeys/etc/ssh
> ssh-keygen -Af hostkeys
> mv hostkeys/etc/ssh/* hostkeys
> rm -r hostkeys/etc
> ```
>
> Then remember to uncomment the related lines inside the [`docker-compose.yml`](docker-compose.yml) file.

To start the Docker-Compose stack in daemon (detached) mode:

```bash
docker-compose up -d
```

Then you can view the logs using this command:

```bash
docker-compose logs -ft
```

### Tips

- :bulb: If you want to **change the resolution** while the container is running, you can use the `xrandr --fb 1024x768` command. The new resolution cannot be larger than the one specified in the `RESOLUTION` environment variable though

## Development

If you want to contribute to this project, you can use the following command to **rebuild the image** and bring up the **Docker-Compose stack** every time you make a change to the code:

```bash
docker-compose down && docker-compose up --build
```
