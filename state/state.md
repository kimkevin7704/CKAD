1. Create busybox pod with two containers, each one will have the image busybox and will run the 'sleep 3600' command. Make both containers mount an emptyDir at '/etc/foo'. Connect to the second busybox, write the first column of '/etc/passwd' file to '/etc/foo/passwd'. Connect to the first busybox and write '/etc/foo/passwd' file to standard output. Delete pod.
    k run busybox --image=busybox --dry-run=client -oyaml -- /bin/sh -c 'sleep 3600' > busybox.yaml

    copy busybox container and rename
    add volumeMounts to both containers at pod.spec.containers
        - mountPath: /etc/foo
          name: volume
    
    add volumes to pod.spec
        - name: volume
          emptyDir: {}
    
    ka busybox.yaml
    k exec -it busybox -c busybox2 -- sh
    get first line of /etc/passwd
    vi etc/foo/passwd
    add line
    exit

    k exec -it busybox -c busybox1 -- sh
    cat /etc/foo/passwd

2. Create a PersistentVolume of 10Gi, called 'myvolume'. Make it have accessMode of 'ReadWriteOnce' and 'ReadWriteMany', storageClassName 'normal', mounted on hostPath '/etc/foo'. Save it on pv.yaml, add it to the cluster. Show the PersistentVolumes that exist on the cluster
    vi pv.yaml
    copy from docs
    accessMode - pv.spec
    10Gi - pv.spec.capacity.storage
    hostPath - pv.spec.hostPath.path
    storageClassName - pv.spec

    ka pv.yaml
    k get pv

3. Create a PersistentVolumeClaim for this storage class, called 'mypvc', a request of 4Gi and an accessMode of ReadWriteOnce, with the storageClassName of normal, and save it on pvc.yaml. Create it on the cluster. Show the PersistentVolumeClaims of the cluster. Show the PersistentVolumes of the cluster


4. Create a busybox pod with command 'sleep 3600', save it on pod.yaml. Mount the PersistentVolumeClaim to '/etc/foo'. Connect to the 'busybox' pod, and copy the '/etc/passwd' file to '/etc/foo/passwd'

5. Create a second pod which is identical with the one you just created (you can easily do it by changing the 'name' property on pod.yaml). Connect to it and verify that '/etc/foo' contains the 'passwd' file. Delete pods to cleanup. Note: If you can't see the file from the second pod, can you figure out why? What would you do to fix that?

6. Create a busybox pod with 'sleep 3600' as arguments. Copy '/etc/passwd' from the pod to your local folder
