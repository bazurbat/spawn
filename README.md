# Spawn

A convenience wrapper script for `docker run` which sets up an environment for
interaction with the host. It takes care of passing the necessary variables,
sockets, bind mounts and so on.

Features:

  * Share host's GPU (/dev/dri)
  * Share host's X11, PulseAudio and KeePassXC sockets
  * Convenience shortcuts to bind host's directories
  * Fetches secrets from user's Secret Storage and exports to /run/secrets
    inside the container.

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
  --with-secret <spec>  Export secrets from Secret Storage
  --with-certs <name>   Export certificates from trust store as <name>

  The <spec> argument to '--with-secret' should have the format:

      name: attribute value [attribute value]...

  The script will lookup a secret matching the specified attribute values using
  secret-tool and mount it as the /run/secrets/<name> inside the container. The
  colon after the name is optional.

```
