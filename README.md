# zeitgeist/docker-calibre

[calibre](http://calibre-ebook.com/) (latest version) in a Docker container.

## Requirements

* [Docker](https://www.docker.com/) 1.5+ (previous versions may work fine, but I haven't tried)
* An X11 socket

## Installation

Get the [trusted build on the docker hub](https://registry.hub.docker.com/u/zeitgeist/docker-calibre/):

```bash
$ docker pull zeitgeist/docker-calibre
```

or download and compile the source yourself from Github:

```bash
$ git clone https://github.com/alexzeitgeist/docker-calibre.git
$ cd docker-calibre
$ docker build -t zeitgeist/docker-calibre .
```

## Usage

### First Start (isolated container)

```bash
$ docker run --rm \
  -v /tmp/.X11-unix:/tmp/.X11-unix:ro \
  -e DISPLAY=unix$DISPLAY \
  zeitgeist/docker-calibre
```

This command has the minimum requirements to create a fully isolated container and to start calibre. It's attached to the host's X11 socket and display. Upon exit, the container will be removed automatically.

### With USB host access

```bash
$ docker run --rm \
  -v /tmp/.X11-unix:/tmp/.X11-unix:ro \
  -e DISPLAY=unix$DISPLAY \
  --privileged \
  -v /dev/bus/usb:/dev/bus/usb \
  zeitgeist/docker-calibre
```

This command is almost identical except that it also allows the container to access USB devices attached to the host. This is important if you want to manage your eBook devices directly from within the calibre container.

### With USB host access and persistent storage (library, settings)

```bash
$ docker run --rm \
  -v /tmp/.X11-unix:/tmp/.X11-unix:ro \
  -e DISPLAY=unix$DISPLAY \
  --privileged \
  -v /dev/bus/usb:/dev/bus/usb \
  -v "${HOME}/Calibre Library/":"/home/user/Calibre Library" \
  -v "${HOME}/.config/calibre":"/home/user/.config/calibre" \
  zeitgeist/docker-calibre
```

Here we added two data volumes that map to folders on our local host. Specifically, in this case:

* `$HOME/Calibre Library` holds our calibre library
* `$HOME/.config/calibre` is where calibre settings are persistently stored

### With 3D acceleration (bonus)

```bash
$ docker run --rm \
  -v /tmp/.X11-unix:/tmp/.X11-unix:ro \
  -e DISPLAY=unix$DISPLAY \
  --privileged \
  -v /dev/bus/usb:/dev/bus/usb \
  -v /dev/dri:/dev/dri:rw \
  -v "${HOME}/Calibre Library/":"/home/user/Calibre Library" \
  -v "${HOME}/.config/calibre":"/home/user/.config/calibre" \
  zeitgeist/docker-calibre
```

This adds the host's 3D / hardware acceleration to the container. Not sure if calibre benefits from it, but at least in my case, it stopped libGL error from spitting out `failed to open drm device` errors.

## Caveats

* External links that would open a browser of file viewer don't work from within the container. 

## Help! I started the container but I don't see calibre

You could have an issue with the X11 socket permissions since the default user used by the `zeitgeist/docker-calibre` image has both user and group id set to `1000`; in that case you can run either create your own base image by modifying `Dockerfile` with the appropriate ids, or run `xhost +` on your machine and try again.
