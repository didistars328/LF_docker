## Container Operations - runC

#### 1. How to create a rootfs directory and start a runC container with it?   
We will need a `rootfs` directory, from which runC would start a container after reading the configuration file. For
simplicity, we will export a Docker container into a folder to create the rootfs directory.    
Create a folder which contains the runC configuration file and the rootfs directory:        
 
    mkdir ~/myrunccontainer
    docker export $(sudo docker create busybox) > /tmp/busybox.tar
    mkdir -p ~/myrunccontainer/rootfs
    tar -C ~/myrunccontainer/rootfs -xf /tmp/busybox.tar
    cd ~/myrunccontainer/rootfs
    ls
    output: bin dev etc home proc root sys tmp usr var    

#### 2. How to generate the runC spec file?   
`runC` uses a configuration file to start a container. A sample configuration file can be created using the spec
subcommand.   

    cd ~/myrunccontainer/
    runc spec
    ls
    output: Config.json rootfs    
  
> Look at the configuration file. In the configuration file we can see sections such as the following:   
> - The process folder, which needs to be started when the container starts:     
>  ```
>  ……
>   “process”: {
>              “terminal”: true,
>               “consoleSize”: {
>              “height”: 0,
>              “width”: 0
>              },
>              “user”: {
>              “uid”: 0,
>              “gid”: 0
>              },
>              “args”: [
>              “sh”
>              ],
>   ……
>  ```         
> - The rootfs folder, which will have the directory structure from which the container would start:
>  ```
>  ……
>  “root”: {
>          “path”: “rootfs”,
>          “readonly”: true
>          },    
>  ……
>  ```          
> - The namespaces folder, which will be created when the container starts:   
>  ```
>  ……
>  “namespaces”: [
>                {
>                 “type”: “pid”
>                },
>                {
>                 “type”: “network”
>                },
>                {
>                 “type”: “ipc”
>                },
>                {
>                 “type”: “uts”
>                },
>                {
>                 “type”: “mount”
>                },
>  ],
>  ……
>  ```

#### 3. How to start a container with runC?   
`runc run busybox`
#### 4. How to list the running containers?
`runc list`
#### 5. How to check the processes running inside the container?
`runc ps busybox`
#### 6. How to check the events of the container?
`runc events busybox`
#### 7. How to pause a container?
```
runc list
runc pause busybox
runc list
```    
#### 8. How to resume a container?
```
runc list  
runc resume busybox
runc list
```
#### 9. How to check the container state in runC?
`runc state busybox`
#### 10. How to delete a container in runC?
`runc delete -f busybox` 
