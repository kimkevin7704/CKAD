1. Create a pod with two containers, both with image busybox and command "echo hello; sleep 3600". Connect to the second container and run 'ls'
    k run busybox-multi --image=busybox --command --restart=Never --dry-run=client -oyaml -- "echo hello; sleep 3600" > busybox-multi.yaml
    copy and paste container spec using V (highlight line) y (yank) p (paste after cursor)
    ka busybox-multi.yaml
    k exec -it busybox-multi -c busybox-multi2 -- /bin/sh
    ls

2. Create a pod with an nginx container exposed on port 80. Add a busybox init container which downloads a page using "wget -O /work-dir/index.html http://neverssl.com/online". Make a volume of type emptyDir and mount it in both containers. For the nginx container, mount it on "/usr/share/nginx/html" and for the initcontainer, mount it on "/work-dir". When done, get the IP of the created pod and create a busybox pod and run "wget -O- IP"
    k run nginx --image=nginx --port=80 --dry-run=client -oyaml > nginx-multi.yaml
    add initContainer with commands
    add volumeMount in nginx and initContainer
    name as same volume name
    make volume
    add containerPort 80 for nginx
    ka nginx-multi.yaml
    k get pod -o wide (get IP *remember to add port later)
    k run nginx-test --image=busybox --restart=Never --rm -it -- /bin/sh -c "wget -O- IP"

