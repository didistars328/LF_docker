## Container Operations - Docker
  
#### 1. How to pull a container image to create a container?        
 `docker image pull alpine`
> By default, the pull command will pull the images from Docker Registry/Hub. We can pull the images from a private
  registry as well:
> `docker image pull <private_registry>:<port>/image`    

#### 2. How to list images?
`docker image ls` or `docker images`
#### 3. How to create a container?   

    docker container create -i -t alpine sh
    output:
    6ac952f2c282216dd871d8f958e82550d4228a0fa24067589fe9eda48f730b69
> The above command will create a container, but it will not start it.
#### 4. How to start a container?
`docker container start 6ac6ac`  
> The above command would start the container we created in the previous step. `6ac...` is the partial container ID for the container we created.    
> We can refer to:
> - container by its ID
> - by its partial ID (as long as it is unique)
> - by its name
#### 5.How to list the container?    
To list the running containers, we can use the docker container ls command: `docker container ls`
#### 6. How to run a container?
`docker container run -i -t --name myalpine alpine sh`
> With the docker container run command we can run a container, which would create and start a container in one go!    
> With the `-t` option we allocate a *pseudo-TTY*, and, with the `-i` option we keep the *STDIN* open for the interactive mode. With the `--name` option we are also giving a name to the myalpine container.
#### 7. How to detach from the running container?
By pressing *Ctrl + p, Ctrl + q* from a running container:
```
docker container run -i -t --name myalpine alpine sh
output:
/ #
(^p^q)
output:
root@nlocal_machine-01:~#
docker container ls
output:
------------------------------------------------------------
CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
a9d171545e62 alpine "sh" 2 minutes ago Up 2 minutes myalpine
```    
#### 8. How to attach to a running container?
```
docker container ls
docker container attach myalpine
```
#### 9. How to run a container in the background?
`docker container run -d alpine /bin/sh -c 'while [ 1
 ]; do echo "hello world from container"; sleep 1; done'`
#### 10. How to see the logs of a container?
`docker container logs be9f86d4d1df`
#### 11. How to stop a running container?
`docker container stop be9f86d4d1df`
#### 12. How to list all the containers (running and stopped)?
`docker container ls -a`
#### 13. How to start a stopped container?
`docker container start be9f86d4d1df`
#### 14. How to restart a container?
`docker container restart be9f86d4d1df`
#### 15. How to pause a container?
`docker container pause be9f86d4d1df`
#### 16. How to unpause a container?
`docker container unpause be9f86d4d1df`
#### 17. How to rename a container?
`docker container rename elastic_yalow while_loop_container`
#### 18. How to remove/delete a container?
```
docker container stop while_loop_container
docker container rm while_loop_container
```
> With the -f flag we can forcefully remove it without stopping it:
> `docker container rm -f myalpine`

#### 19. How to automatically remove the container when it exits?
```
docker container run --rm --name container_with_rm alpine ping -c 3 google.com
output:
---------------------------------------------------------
PING google.com (172.217.2.14): 56 data bytes
64 bytes from 172.217.2.14: seq=0 ttl=58 time=1.780 ms
64 bytes from 172.217.2.14: seq=1 ttl=58 time=1.682 ms
64 bytes from 172.217.2.14: seq=2 ttl=58 time=1.870 ms
--- google.com ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 1.682/1.777/1.870 ms

docker container ls -a
output:
---------------------------------------------------------------------------
CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
6ac952f2c282 alpine "sh" About an hour ago Up About an hour admiring_liskov
```
#### 20. How to set the hostname for a container?
By adding the `-h` option to the run subcommand, we can set the hostname for a container:   
`docker container run -h alpine -it --rm alpine sh`
#### 21. How to set the current working directory for a container?
With the `-w` option added to the run subcommand, we can set the current working directory for the container:    
`docker container run -it -w /var --rm alpine sh`
#### 22. How to set the environment variable to a container?
`docker container run -it --env "WEB_HOST=172.168.1.1" --rm alpine sh`
#### 23. How to set the ulimit for a container?
With the `--ulimit` option added to the run subcommand, we can set the ulimit for the container:    
    
    docker container run -it --ulimit nproc=10 --rm alpine sh
    output:
    /#
    /# ulimit -a
    output:
    -f: file size (blocks) unlimited
    -t: cpu time (seconds) unlimited
    -d: data seg size (kb) unlimited
    -s: stack size (kb) 8192
    -c: core file size (blocks) unlimited
    -m: resident set size (kb) unlimited
    -l: locked memory (kb) 64
    -p: processes 10
    -n: file descriptors 1048576
    -v: address space (kb) unlimited
    -w: locks unlimited
    -e: scheduling priority 0
    ...
> In the above case, we have set the number of processes this container can create.
#### 24. How to get all the details about a container?
`docker container inspect 6ac952f2c282`
#### 25. How to bind a container to run a given set of CPU(s) of the host?
We can use the `--cpuset-cpus` option of the docker container run command to bind a container to run a given CPU or set of CPUs of the host. In the following example we are binding the container to run only on CPU `0`    
`docker container run -d --name cpulimitAlpine --cpuset-cpus="0" alpine top`
> We can verify the setting from the docker container inspect command:
> ```
> docker container inspect cpulimitAlpine | grep -i cpu
> output:
> "Name": "/cpulimitAlpine",
>     "CpuShares": 0,
>     "NanoCpus": 0,
>     "CpuPeriod": 0,
>     "CpuQuota": 0,
>     "CpuRealtimePeriod": 0,
>     "CpuRealtimeRuntime": 0,
>     "CpusetCpus": "0",
>     "CpusetMems": "",
>     "CpuCount": 0,
>     "CpuPercent": 0,
> ```
#### 26. How to limit the memory of a container?
We can use the `--memory` option of the docker container run command to limit the memory of a container:    
`docker container run -d --name memorylimitAlpine --memory "200m" alpine top`
> To verify the settings:
> ```
> docker container inspect memorylimitAlpine | grep -i Mem
> output:
> "Name": "/memorylimitAlpine",
>     "Memory": 209715200,
>     "CpusetMems": "",
>     "KernelMemory": 0,
>     "MemoryReservation": 0,
>     "MemorySwap": -1,
>     "MemorySwappiness": -1,
> ```
#### 27. How to create a new process inside a running container?
Es example we can run `ip` command inside a container:   
`docker container exec 6ac952f2c282 ip a`
#### 28. How to set the restart policy to a container?
`docker container run -d --restart=always --name web1 nginx`
> If we want to limit the times of restart, then we can set the policy as following:    
> `docker container run -d --restart=on-failure:3 --name web2 nginx`
#### 29. How to copy files between the host system and a running container?
Copy the file from the host to the container:    
`docker container cp index.html web2:/usr/share/nginx/html/`
#### 30. How to label a container?
`docker container run -d --label env=dev nginx`
#### 31. How to use filters to list container?
`docker container ls --filter label=env=dev`
#### 32. How to remove/delete all the containers with one command?
With the `-q` option to the `docker container ls` command we can get only the container IDs of containers:    

    docker container ls -q
    
    output:
    745ca161c088
    f8cbbf549811
    16f19a154391
    6ac952f2c282
To remove all running and stopped containers we can use the following command:    
``docker container rm -f `docker container ls -q` ``
#### 33. How to change the default executable which runs when the container starts?
`docker container run -it nginx sh`
#### 34. How to get privileged access inside the container?
With the privileged mode, we can give extended privileges to the container to access the devices. By default, it is
not enabled. To enable it, we need to pass the `--privileged` option with the `docker container run` command.     
`docker container run -it --net=host --privileged alpine sh`
#### 35. How to remove all stopped containers?
`docker container prune`   
    