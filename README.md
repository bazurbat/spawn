# Spawn

A convenience wrapper script for `chroot`, `systemd-nspawn` or `docker run`
which sets up an environment for interaction with the host. It takes care of
passing the necessary variables, sockets, bind mounts and file permissions.

Features:

  * Sharing SSH agent
  * Sharing X11
  * Sharing PulseAudio
  * Mounting the specified directory as home or work
  * Remapping of files/permissions if UIDs differ from the host
  * Running as a specified user or root
  * Changing reported arch when running in chroot

## Options

```
Usage: spawn [OPTIONS...] <name|directory> [--] [COMMAND [ARGS...]]

  Spawns a new container or chroot and configures a shared environment.

OPTIONS:

  -h|--help             Print this help message
  -r|--as-root          Use superuser inside the spawned root
  -n|--dry-run          Do not run any privileged commands - only print them
  --user <name>         Assume <name> user inside the container
  --arch <arch>         Set architecture reported by uname(2)
  --bind-home <dir>     Bind the specified directory as a user's home directory
  --bind-dir <dir>      Mount the specified directory inside the root
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

  If neither the '--as-root' nor the '--user' option is specified a user inside
  the spawned environment is assumed to be 'dev'.

  Use the '--bind-home' option to mount the specified directory as a user's home
  directory inside the chroot. For example, if you run:

      spawn --bind-home $HOME/somedir root

  The directory '$HOME/somedir' will become '/home/dev' inside the spawned
  chroot (assuming the default 'dev' user is not overridden). Use this to have a
  separate clean profile and settings from the host or to share the same home
  directory between different chrooted environments.

  Use the '--bind-dir' option to mount the specified directory inside the chroot
  at the same path as outside. Run build commands relative to this directory to
  have compile_commands.json generated with the absolute paths directly usable
  by editor and language servers outside of the chrooted environment. After
  spawning the chroot it will be the current working directory and also
  available as '\$WD' in the environment. It can be the same directory which is
  specified for '--bind-home'.

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

## Init Ubuntu Root

A companion script `init-ubuntu-root` allows to quickly initialize Ubuntu chroot
for development.

```
Usage: init-ubuntu-root [OPTION...] <root-dir>

  Initializes unpacked Ubuntu rootfs for chroot or spawn.

OPTIONS

  -h|--help                Show help
  --init                   Minimal init
  --default                Default init
  --dev                    Development environment init
  --with-backports         Enable backports APT source
  --with-i386              Add i386 arch
  --with-dev-tools         Install development tools
  --with-java8             Install Open JDK 8
  --with-wine              Install Wine
  --with-perforce          Install Perforce
  --skip-update            Do not run 'apt-get update' before install
  --add-user               Add dev user

  The <root-dir> argument is mandatory and should point to unpacked Ubuntu root
  directory. Default tweaks are taken from Docker debootstrap setup. The
  selection of development tools is from a personal list collected over time.

  This script creates and modifies files in the system areas inside the
  <root-dir>, and the running user should have the appropriate privileges to do
  so. This usually means that it should be run as root (sudo and such).

  The default setup means:

    * Run deboostrap steps with minor adjustments
    * Copy /etc/resolv.conf to the <root-dir>
    * Add LANG=C.UTF-8 config to profile.d
    * Optionally add proxy config to profile.d and apt.conf.d
    * Run 'apt-get update' then 'apt-get dist-upgrade'
    * Add dev user

  NOTE: proxy configuration is added only if http_proxy or https_proxy
  variables are set in the current environment.

IMAGES

  Images can be downloaded from:

    https://partner-images.canonical.com/core/trusty/current/ubuntu-trusty-core-cloudimg-amd64-root.tar.gz
    https://partner-images.canonical.com/core/xenial/current/ubuntu-xenial-core-cloudimg-amd64-root.tar.gz

  Unpack with:

    mkdir <root-dir>
    sudo tar -C <root-dir> -xpf <filename>
```
