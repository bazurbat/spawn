# Spawn

A convenience wrapper script for `chroot`, `systemd-nspawn` or `docker run`
which sets up an environment for interaction with the host. It takes care of
passing the necessary variables, sockets, bind mounts and file permissions.

Features:

  * Running as a specified user or root
  * Remapping of files/permissions if UIDs differ from the host
  * Changing reported arch when running in chroot
  * Bind mount the specified directory as home
  * Sharing SSH agent
  * Sharing X11
  * Sharing PulseAudio

## Options

```
Usage: spawn [OPTIONS...] <name|directory> [--] [COMMAND [ARGS...]]

  Spawns new container or chroot and configures shared environment.

OPTIONS:

  -h|--help             Print this help message
  -r|--as-root          Use superuser inside the spawned root
  -n|--dry-run          Do not run any privileged commands - only print them
  --user <name>         Assume <name> user inside the container
  --arch <arch>         Set architecture reported by uname(2)
  --bind-home <dir>     Bind the specified directory as user home directory
  --with-ssh-agent      Pass SSH_AUTH_SOCK variable and bind the socket
  --with-x11            Pass DISPLAY, XAUTHORITY and bind $xsock_dir
  --with-pulseaudio     Set PULSE_SERVER and pass Pulseaudio socket
  --to-stderr           Redirect all container command output to stderr
  --share-devices       Share devices with the host
  --using-docker        Run command in a new Docker container
  --using-chroot        Change the root directory using chroot
  --cleanup             Release mounts and unlock the root directory

SYNOPSIS:

  Unknown options before <name|directory> are passed to the selected launcher
  as arguments (docker run, chroot, etc.). Everything else after
  <name|directory> and optional '--' are considered the command to be invoked
  inside the spawned container or chroot.

  Options starting with '--using' are mutually exclusive -- only the last one
  will have an effect. If none was given and the first non option argument
  specifies an existing directory then chroot will be assumed, otherwise the
  name will be passed to 'docker run'.

NOTE:

  If you get "Connection failure: Protocol error" error or silent audio when
  trying to run PulseAudio applications inside the chroot, you need to disable
  communication through shared memory for the client. Add:

    enable-shm = no

  to "/etc/pulse/client.conf" inside the root directory.
```
