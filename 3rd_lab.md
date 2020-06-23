## Container Runtimes

### runC
Install *runC*:
`apt-get install runc`

Verify the installation by running the following command:
`runc --version`

### Docker
Install Docker:
`curl -fsSL https://get.docker.com/ | sh`

Start, stop, restart, and see the status of the Docker daemon:
```access transformers
systemctl start docker
systemctl stop docker
systemctl restart docker
systemctl status docker
```

### Rkt
install rkt: `git clone https://github.com/coreos/rkt` then `cd rkt`

Run the installation script: `/scripts/install-rkt.sh`
