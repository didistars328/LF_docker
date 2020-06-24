## Container Operations - rkt

#### 1. How to fetch an image?   
With the rkt fetch command we can fetch images:    
`rkt fetch coreos.com/etcd:v2.0.0`  
> By default, all the images are GPG-signed. We need to trust the keys before the image gets downloaded.
> Rkt images can be hosted on any webserver. To host a rkt image, we need to put the image, its signature,
> and its public key on that server. We also need to add the metadata in the html file of the served page
> with the details of the image.
#### 2. How to download a Docker image?
As Docker images are not GPG-signed as rkt expects, we need to pass the `--insecure-options=image`
option while fetching the image, with which we don't check for the GPG signature:   

    rkt fetch --insecure-options=image docker://busybox
    rkt image list  

#### 3. How to start a pod? 
With the rkt run command we can start a pod:
> `SYNOPSIS rkt run [--volume=name,kind=host,...] [--mount volume=VOL,target=PATH]
> IMAGE [-- image-args...[---]]...`     

`rkt run --insecure-options=image docker://nginx`
#### 4. How to list the rkt pods? 
`rkt list`
#### 5. How to set the custom APP name for a pod?
We can give a custom APP name with the --name option to the rkt run command, as following:    
`rkt --insecure-options=image run docker://busybox --name=busybox1`
#### 6. How to change the default executable with the pod?
When the container starts, it runs with some default executable, if defined. For example, a container with the nginx 
image would start with `/usr/sbin/nginx -g daemon off`. We can change the default executable with the 
`--exec` flag to the rkt runcommand. In the following pod we are changing the default executable to `/bin/date` 
and exit:    
`rkt --insecure-options=image run docker://nginx --exec /bin/date`
#### 7. How to set environment variables while starting the pod?
With the `--set-env` option to the `rkt run` command, we can set the environment variables for the pod. In the 
following example, we are setting `WEB=172.16.28.19`:    
`rkt --insecure-options=image run docker://busybox --set-env=WEB=172.16.28.19 --exec env`
#### 8. How to start a pod in the daemon mode with systemd?
We can use systemd to start a pod in the daemon mode. In the following example, we are using systemd-run 
to create a service from the pod:
```
systemd-run --slice=machine rkt run --insecure-options=image docker://nginx
output:
Running as unit run-r87e099d509394683a7a1cc7e55d74882.service.
```
The above command would create a systemd service, which we can manage like any other systemd service. In
the following example, we are looking at the status:
```shell script
systemctl status run-r87e099d509394683a7a1cc7e55d74882.service
output:
● run-r87e099d509394683a7a1cc7e55d74882.service - /usr/bin/rkt
--insecure-options=image run docker://nginx
 Loaded: loaded
Transient: yes
 Drop-In: /run/systemd/system/run-r87e099d509394683a7a1cc7e55d74882.service.d
          └─50-Description.conf, 50-ExecStart.conf, 50-Slice.conf
 Active: active (running) since Sat 2016-12-31 00:31:14 UTC; 7s ago
 Main PID: 31707 (ld-linux-x86-64)
....
```
> Pods created via systemd would also be listed with the `rkt list` command.   

We can stop the systemd service, which would stop the pod as well:
`systemctl stop run-r87e099d509394683a7a1cc7e55d74882.service`
#### 9. How to enter into a running pod?
With the `rkt enter` command we can enter into an already running pod. This is similar to the `docker exec` 
command
```
rkt list
rkt enter 29614ed8
```
#### 10. How to stop a pod?
`rkt stop 29614ed8` 
#### 11. How to remove a pod?
`rkt rm 29614ed8`
