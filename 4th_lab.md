## Accessing a Remote Docker Daemon

#### 1. How to start a Docker daemon to listen for remote connection requests?  
By default, docker daemon listens on the /var/lib/docker.sock UNIX socket, which will not allow access
over the network. To make it listen over the network, we can use either one of the ways mentioned:   
> ANY OF THIS METHODS !!!
- Run `dockerd` (i.e docker daemon binary) manually by using a specific `ip:port`
option   
  ```
  systemctl stop docker    
  /usr/bin/dockerd -H 0.0.0.0:2375
  ```    
- Change the `docker.service` (*/lib/systemd/system/docker.service*) file or create/update the daemon configuration file `/etc/docker/daemon.json` to load by systemd while starting it   
  ```access transformers
  ...
  [Service]
  Type=notify
  # the default is not to use systemd for cgroups because the delegate issues still
  # exists and systemd currently does not support the cgroup feature set required
  # for containers run by docker
  ExecStart=/usr/bin/dockerd -H fd:// -H tcp://0.0.0.0:2375 --containerd=/run/containerd/containerd.sock
  ExecReload=/bin/kill -s HUP $MAINPID
  TimeoutSec=0
  ...
  ```   
  ```
  systemctl daemon-reload    
  systemctl restart docker.service
  ```
#### 2. How to connect to a remote Docker daemon?
>  Make sure you have Docker installed on the system, from which you will connect to the remote
  Docker daemon.    

To connect with the remoteDocker daemon, we can do it with one of the following (**ANY OF THESE METHODS SHOULD WORK**):    
-  From your host (workstation) by providing the -H option with the docker command line:
   `docker -H tcp://162.243.83.46:2375 pull alpine`
- By using the `DOCKER_HOST` environment variable: `export DOCKER_HOST="tcp://162.243.83.46:2375"`    
#### 3. How to use docker-machine to create a Docker instance on DigitalOcean and then, connect to it?    
***Docker Machine*** is a tool with which we can install Docker Engine on virtual hosts and then manage them. With this
tool, we can create Docker hosts on our local Linux/Mac/Windows box, on cloud providers, like AWS or
DigitalOcean, etc.   
- Download Docker Machine
  ``` 
  curl -L https://github.com/docker/machine/releases/download/v0.16.2/docker-machine-`uname -s`-`uname -m` >/usr/local/bin/docker-machine && \
  chmod +x /usr/local/bin/docker-machine
  ```
- Get the access token from your DigitalOcean account to authenticate automatically
    - use this link to generate one: [API_DigitalOcean](https://www.digitalocean.com/docs/apis-clis/api/create-personal-access-token/)   
    - export token to a sys variable:
    `export DO_TOKEN=ed675a93a5e9be1cde0232358ca324e61e8ca5c5913182e1d7666cbadfea2ab0`   

#### 4. Create a Docker host on DigitalOcean using docker-machine, where demo would be our Docker host name.
`docker-machine create --driver digitalocean --digitalocean-access-token=$DO_TOKEN
 demo`
> With the driver option, we provide the provider on which we will create the VM and install Docker. virtualbox,
  aws, digitalocean, etc., are supported drivers.

#### 5. How to connect to the newly created Docker host from the Docker client?   
   
- List the configuration variables for the newly created host   
  `docker-machine env demo`
- Export the configuration variables as environment variables, so Docker client can connect to it   
  `eval $(docker-machine env demo)`
- Run docker container as example    
          
      docker ps
      docker run -d nginx

#### 6. Create another Docker host on DigitalOcean - demo1    
`docker-machine create --driver digitalocean --digitalocean-access-token=$DO_TOKEN
 demo1`
 
#### 7. How to list all the instances created by `docker-machine`?    
`docker-machine ls`
> By `*` you can see which of them is active one:  
> ```
> NAME    ACTIVE   DRIVER         STATE     URL                         SWARM   DOCKER      ERRORS
> demo    *        digitalocean   Running   tcp://45.55.48.186:2376             v19.03.11   
> demo1   -        digitalocean   Running   tcp://134.209.166.26:2376           v19.03.11

#### 8. Changes between Docker machines:    
`eval $(docker-machine env demo1)`
You should see that asterisk is now in front of demo1
```
NAME    ACTIVE   DRIVER         STATE     URL                         SWARM   DOCKER      ERRORS
demo    -        digitalocean   Running   tcp://45.55.48.186:2376             v19.03.11   
demo1   *        digitalocean   Running   tcp://134.209.166.26:2376           v19.03.11   
```

#### 9. How to delete the demo instance we created earlier?    
`docker-machine rm demo`

  
    
