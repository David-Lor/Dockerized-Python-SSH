# Dockerized Python+SSH image 🐍🔐🐳

## Objective

This container provides the base Python container with a SSH server preinstalled that can be used
to connect to the Python interpreter and upload files remotely, i.e. using PyCharm.

## How does it work?

By default, a non-root user, created on the Dockerfile, is used for normal operations.
Is recommended to connect SSH using this non-root user, using the username `pythonssh` and the password `sshpass` by default.

## How to deploy?

```bash
docker run -d -p 2222:22 --name pythonssh davidlor/python-ssh
```

- Change `-p 2222:22` if you want to change the exposed port on the host machine (2222 to other prefered port)
- Change the `--name` "pythonssh" if you want

### Connecting with root

If required, root user can be connected through SSH. However, you must set root password manually, following these steps:

- Enable root login on SSH (from within the container):
    - Deploy a Docker container using this image as usual
    - Run bash on it as root: `docker exec -it -u root pythonssh bash` (replace `pythonssh` with the container name or ID)
    - Once you are inside the container, run: `echo "PermitRootLogin yes" >> /etc/ssh/sshd_config`
    - Restart SSH service running `service ssh restart`
- Set a password for root:
    - Once you are inside the container, run `passwd` and you will be asked for a new password for the user root
    - Run `exit` when done to quit the container
- Now you can login with root through SSH

## Mountpoints

Mountpoints are defined on Dockerfile, so volumes will be automatically created if you do not define them.

- You can mount the .ssh directory for the non-root user, to manage the valid SSH keys used to connect through SSH.
    - Location: `/home/pythonssh/.ssh` (being `pythonssh` the username of the non-root account)
- You can mount the /etc/ssh directory for keeping the ssh config files on a persistent volume
    - Location: `/etc/ssh`

## Usage

Notice that the latest Python executable is located at `/usr/local/bin/python`. You must input this route i.e. in PyCharm.

[Usage instructions for PyCharm](https://www.jetbrains.com/help/pycharm/configuring-remote-interpreters-via-ssh.html)

## How to build?

You can build your own image using this repository. This can be useful for changing the username/password or building for other platforms.

```bash
git clone https://github.com/Pythoneiro/Dockerized-Python-SSH.git
docker build -t "local/python-ssh" --build-arg USERNAME=pythonssh --build-arg USERPASS=sshpass Dockerized-Python-SSH
```

- change `local/python-ssh` by your custom desired image name
- change `pythonssh` by your custom desired username
- change `sshpass` by your custom desired user password

## TODO

- Customize user/password by ENV variables or "secret file"
- Set SSH log level for getting an output on `/var/log/auth.log`
- Install sudo
