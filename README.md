# Spawn

A convenience wrapper script for `docker run`, `systemd-nspawn` or `chroot`
which setups shared environment for easier interaction with the host.

The shared environment includes:

* SSH agent forwarding
* X11 socket with X authentication support
* Pulseaudio (currently broken)

*Warning*: the project is just published and due to emerging new use cases the
code is constantly in flux.
