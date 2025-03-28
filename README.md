# Docker Under the Hood

Containers are isolated processes that run on a virtual machine bundled with Docker Desktop.

### You can visualize this by doing the following:

1. Open a Terminal.
1. Run `docker run -it --rm ubuntu sh -c "sleep 60 & exec bash"`
1. Inside the container, run `ps aux` and note the low number processes.
1. Dive into the VM by running this in a separate terminal:
```
    docker run -it --privileged --rm \          
    --pid=host \
    debian \
    nsenter -t 1 -m -u -n -i sh
```
1.  This works since the pid is set to "host". Run `ps aux | grep "sleep 60"` and note the sleep 60 command and its high process number.
1.  Finally, open a third terminal window and run `ps aux | grep "sleep 60"` to see there is no sleep 60 command, only a process for "grep sleep 60". This is because the command is running in a container, a process running on the VM, not the laptop on which you installed Docker Desktop.

### See the Union File System
You can run `mount` on your container running as the VM to see the various filesystem snapshots. This is where the files are stored that are in your containers.

1. In your container, run `touch test.txt`
1. On your computer, run `docker ps` to get the container ID of a file you modified.
1. On the VM container, run `mount grep | "{CONTAINER_ID"`
1. Then, take the output of that command and find the upperdir location. This should be something like `/var/lib/desktop-containerd/daemon/io.containerd.snapshotter.v1.overlayfs/snapshots/{SOME_NUMBER}/fs`
1. Run `ls {DIRECTORY_FROM_LAST_STEP}` and you will see the file you created.
1. You can see that the lower directory does not show this file, since it was written in the container itself, and will be destroyed when the container is removed.
