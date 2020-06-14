##Building Block

###Namespaces
Namespaces are a feature of the Linux Kernel, and are used to isolate and virtualize system resources between
processes. System resources that can be virtualized are:
- Mount (mnt)
    - It is used to isolate mount points
    - It is similar to chroot, but with improved security.
- Process ID (pid)
    - It is used to isolate PIDs
    - We can have the same virtual PIDs in different namespaces
    - Each virtual PID is mapped to a different PID on the host system.
- Network (net)
    - `/proc/net`, IPs, interfaces, and routes are isolated between network namespaces.
- Interprocess Communication (ipc)
    - SystemV IPC and POSIX Message Queues can be isolated
- UTS (hostname)
    - It is used to isolate the hostname and the NIS (Network Information Service) name between
namespaces.
- User ID (user)
    - User and group IDs are different inside and outside of namespaces and can be duplicated.
####1. How to list all the existing namespaces for a process?
``ls -l /proc/<pid>/ns``

####2. How to create new network namespaces and connect them?
``ip netns add ns#`` ex: `ip netns add ns1 &&  ip netns add ns2`

Create a Veth pair with two interfaces: tap1 and tap2:

``ip link add tap1 type veth peer name tap2``

Attach one endpoint of the veth pair to one of the namespaces:

``ip link set tap1 netns ns1 && ip link set tap2 netns ns2``

Bring up the link for both interfaces:

``ip netns exec ns1 ip link set dev tap1 up && ip netns exec ns1 ip link set dev tap1 up``

Assign the IP address to each one of the interfaces, and then p ing the other endpoint:

`ip netns exec ns1 ifconfig tap1 192.168.1.1 up`
 
 `ip netns exec ns2 ifconfig tap2 192.168.1.2 up`
 
 `ip netns exec ns2 ping 192.168.1.1`
 
 `ip netns exec ns1 ping 192.168.1.2`
 
####3. How to delete a namespace?
`ip netns delete ns#` ex: `ip netns delete ns1 && ip netns delete ns2`

---

###Control Groups (cgroups)

Control Groups (cgroups) are features of the Linux kernel which limit, account, and isolate resource usage of the
following resources to a process group:
- CPU (cpu/cpuset)
    - Controls the time period (microseconds per second) a group should have CPU access
    - Controls the upper limit of CPU time per second for the group
    - Assigns a proportional value of the relative CPU time for a group
    - Assigns chores to the group
    - Controls the memory nodes a group can access.
- Memory (memory)
    - Controls the maximum memory limit for a group
    - Controls memory swappiness, OOM, etc.
- Disk I/O (blkio)
    - Assigns proportional value of block I/O to a group
    - Sets per device hard limits on block I/O access.
- Network (net_cls/net_prio)
    - Tags network packets with a class ID
    - Uses tc to prioritize tagged packets
    - Assigns per interface weighted proportional priority on egress traffic.
- Devices
    - Controls the device access to groups.
- Hugepages
    - Controls hugepages size usage
    - Manages per cgroups hugepages matrices.
    
####1. How to list the available cgroups?
Install the cgroup-tools tool
`apt install cgroup-tools -y`

List the cgroups with the lscgroup command:

`lscgroup`
####2. How to list the cgroups associated with a process?

`cat /proc/<pid>/cgroup` ex: `cat /proc/1/cgroup`

####3. How to freeze a process using cgroups, and then de-freeze it?

> In this lab, we will use the freezer cgroup to freeze and de-freeze a process. Once a process is frozen, we cannot
  do any operation on it. We need to de-freeze it to make the process accessible again.

**Example:**

Create a new cgroup hierarchy under `/sys/fs/cgroup/freezer/`:

```
cd /sys/fs/cgroup/freezer
mkdir mycgroup
cd mycgroup/
ls
output:
cgroup.clone_children cgroup.procs freezer.parent_freezing freezer.self_freezing
freezer.state notify_on_release tasks
```
As soon as the directory was created under the freezer cgroup, some files have been populated automatically by
the cgroup subsystem. The tasks file contains the pids of the processes which will get affected by this cgroup.

```
pwd
output: /sys/fs/cgroup/freezer/mycgroup
cat tasks
output: *empty file*
```
Create a new process and attach it to the c group created in the previous step:
- Open a new terminal and get its PID
  ```
  ps
  output:
  PID TTY TIME CMD
  6664 pts/1 00:00:00 bash
  6693 pts/1 00:00:00 ps
  ```
- Come back to the previous terminal and, in the tasks file, append the PID which we got in previous step:
  ```
  pwd
  output: /sys/fs/cgroup/freezer/mycgroup
  echo 6664 >> tasks
  ```
- Run the following command to freeze all processes:
  ```
  echo FROZEN > freezer.state
  ```
- Go back to the other terminal and try to write something on the screen. Nothing comes on the screen, as it is
  frozen
- Run the following command to de-freeze all the processes on the terminal from which we froze all the processes
  ```
  echo THAWED > freezer.state
  ```
- Now, go back to the other terminal and you will see that whatever we typed earlier on the frozen terminal is shown, because the processes are now thawed
    
---

###UnionFS
UnionFS transparently overlays files and directories of separate filesystems, to create a coherent filesystem. Each
one of the participant directories is referred to as a branch. We can set the priority while mounting branches, mount
them read-only, etc.

####1. How to transparently overlay two directories, one on top of the other, using UnionFS?
Install the unionfs-fuse package `apt install unionfs-fuse`

Create a dir1 directory, and then, create two files, f1 and f 2, inside that directory

```
mkdir /root/dir1 && \
touch /root/dir1/f1 && \
touch /root/dir1/f2
```

Create a dir2 directory, and then, create two files, f3 and f 4, inside that directory
```
mkdir /root/dir2 && \
touch /root/dir2/f3 && \
touch /root/dir2/f4
```

Create a directory called union `mkdir /root/union`.
Mount `dir1` and `dir2` to the union directory using `unionfs-fuse`, and then, list the files:

`unionfs /root/dir1:/root/dir2 /root/union/`

`ls /root/union/`