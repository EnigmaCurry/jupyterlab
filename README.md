# enigmacurry/jupyterlab

This is my custom jupyter lab container.

 * Run in userspace with systemd and podman
 * Builds your own container from base images
 * Dark theme
 * Emacs keybindings
 * My [spacemacs config](https://github.com/EnigmaCurry/emacs) for Emacs in the terminal
 * Electron app for desktop experience and full keybinding support

# Setup podman

 * Install [podman](https://wiki.archlinux.org/index.php/Podman)
   * podman is just like docker, but unlike docker, it can run without root.
   * containers are managed by systemd (no docker daemon)
 * You can use your regular user account, or create a new user account. For this
   example it's `ryan`. If you need to create this user, run:

```
useradd -m ryan
```

 * Allow the `ryan` account to start systemd (user) services on boot:

```
loginctl enable-linger ryan
```

* Setup the `ryan` account for running containers:
   * Edit `/etc/subuid` (If it does not exist, create a new file):
     * add the line: `ryan:100000:65536`
   * Edit `/etc/subgid` (If it does not exist, create a new file):
     * add the line: `ryan:100000:65536`
   * **reboot** in order to reload this config (not sure why)
   * `ryan` can now use podman and create containers, all running in his own
     user account namespace (rootless).

# Install jupyterlab as a systemd user service

Clone this repository:

```
DIR=$HOME/git/vendor/enigmacurry/jupyterlab
git clone https://github.com/EnigmaCurry/jupyterlab.git $DIR
```

Build the container:

```
podman build -t enigmacurry/jupyterlab $DIR
```

Install the systemd service:

```
mkdir -p $HOME/.config/systemd/user
ln -s $DIR/jupyterlab.service $HOME/.config/systemd/user
systemctl --user enable --now jupyterlab
```

Access the logs of jupyterlab container:

```
journalctl --user --unit jupyterlab
```

If everything starts correctly, you should see a log message containing the URL
and token you need in order to login the first time. For example:

```
http://127.0.0.1:10000/?token=XXXXXX
```

Open your browser to this URL and login.

## Setup jupyter-electron

To fully support emacs keybindings, you cannot run jupyter lab in a regular web
browser. You can instead use an electron based app I've created called
[jupyter-electron](https://github.com/EnigmaCurry/jupyter-electron#readme).
Follow the documentation at that link to get setup.
