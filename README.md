# Dockerized calibre

```console
$ docker run -it \
  -v /tmp/.X11-unix:/tmp/.X11-unix:ro \
  -e DISPLAY=unix$DISPLAY \
  zeitgeist/calibre

$ docker run -it \
  -v /tmp/.X11-unix:/tmp/.X11-unix:ro \
  --privileged \
  -v /dev/bus/usb:/dev/bus/usb \
  -e DISPLAY=unix$DISPLAY \
  zeitgeist/calibre
```
