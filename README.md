# Spawn

A convenience wrapper script for `docker run` or `chroot` which setups shared
environment for interaction with the host to allow running desktop applications
or sharing home directory.

The shared environment includes:

* SSH agent forwarding
* X11 socket with X authentication support
* Pulseaudio
