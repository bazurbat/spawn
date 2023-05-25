# Spawn

A convenience wrapper script for `docker run` which sets up an environment for
interaction with the host. It takes care of passing the necessary variables,
sockets, bind mounts and so on.

Features:

  * Share host's GPU (/dev/dri)
  * Share host's X11, PulseAudio and KeePassXC sockets
  * Convenience shortcuts to bind host's directories

## Options

```
Usage: spawn [OPTIONS...] <image> [COMMAND [ARGS...]]

  -h, --help            Show usage information
  -n, --dry-run         Do not run, only print the resulting command
  --using-docker        Use docker run (default)
  --using-podman        Use podman run
  --name <name>         Set the name of the container
  --as-root             Pass --user=root to the runner
  --user <user>         Set user's HOME to /home/<user>
  --bind <path>         Mount the specified directory inside the container
  --bind-home <path>    Mount the specified directory as /home/<user>
  --with-gpu            Share host's GPU (/dev/dri)
  --with-x11            Share host's X11 socket and pass Xauthority
  --with-pulseaudio     Share host's PulseAudio socket and cookie
  --with-keepassxc      Share host's KeePassXC socket

```
