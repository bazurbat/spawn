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
  --using-docker        Use Docker to spawn the named container
  --using-nspawn        Use 'systemd-nspawn' to chroot
  --using-chroot        Use the regular 'chroot'
  --unlock              Remove the lock file
  --cleanup             Release stale mount points and unlock
  --runtime-dir <dir>   Set runtime dir

SYNOPSIS:

  Unknown options before <name|directory> are passed to the selected launcher
  as arguments (docker run, chroot, etc.). Everything else after
  <name|directory> and optional '--' is considered the command to be invoked
  inside the spawned container or chroot.

  If no '--using-*' option was given and the first non-option argument is an
  existing directory then 'nspawn' will be used if 'systemd-nspawn' was found
  in PATH, otherwise 'chroot'. If the argument is not a directory it is assumed
  to be the name of a Docker container.

  Use the '--unlock' option to remove stale lock files from the root directory
  after an unexpected system shutdown, for example.

  The '--cleanup' option can be used to unmount the directories left by other
  spawn invocations. Use the '--runtime-dir' option to explicitly set the
  runtime directory (\$XDG_RUNTIME_DIR/spawn.*). This is used for
  troubleshooting the script itself and should not be necessary under normal
  circumstances.

  If either '--unlock' or '--cleanup' is specified all other options are
  ignored, the script exits immediately after the cleanup.

NOTE:

  If you get "Connection failure: Protocol error" error or silent audio when
  trying to run PulseAudio applications inside the chroot, you need to disable
  communication through shared memory for the client. Add:

    enable-shm = no

  to "/etc/pulse/client.conf" inside the root directory.

```
