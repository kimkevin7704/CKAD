1. Create an nginx pod with a liveness probe that just runs the command 'ls'. Save its YAML in pod.yaml. Run it, check its probe status, delete it.
    k run nginx-lp --image=nginx --dry-run=client -oyaml > nginx-lp.yaml
    add livenessProbe to pod.spec.containers
        exec:
            command: ["ls"]

2. Modify the pod.yaml file so that liveness probe starts kicking in after 5 seconds whereas the interval between probes would be 5 seconds. Run it, check the probe, delete it.
    add periodSeconds: 5 and initialDelaySeconds: 5 to pod.spec.containers.livenessProbe

3. create an nginx pod (that includes port 80) with an HTTP readinessProbe on path '/' on port 80. Again, run it, check the readinessProbe, delete it.
    k run nginx-http --image=nginx --port=80 --dry-run=client -oyaml > nginx-http.yaml
    add to pod.spec.containers
        readinessProbe:
            httpGet:
                path: /
                port: 80

4. Lots of pods are running in qa,alan,test,production namespaces. All of these pods are configured with liveness probe. Please list all pods whose liveness probe are failed in the format of <namespace>/<pod name> per line.
    I DUNNO LOL

5. Create a busybox pod that runs i=0; while true; do echo "$i: $(date)"; i=$((i+1)); sleep 1; done. Check its logs
    k run busybox --image=busybox --dry-run=client -oyaml -- /bin/sh -c 'i=0; while true; do echo "$i: $(date)"; i=$((i+1)); sleep 1; done' > busybox.yaml

6. Create a busybox pod that runs 'ls /notexist'. Determine if there's an error (of course there is), see it. In the end, delete the pod
    k run busybox-error --image=busybox --dry-run=client -oyaml -- /bin/sh -c 'ls /notexist' > busybox-error.yaml
    k logs busybox-error
    kd pod busybox-error

7. Create a busybox pod that runs 'notexist'. Determine if there's an error (of course there is), see it. In the end, delete the pod forcefully with a 0 grace period
    k run busybox-notexist --image=busybox --dry-run=client -oyaml -- /bin/sh -c 'notexist' > busybox-notexist.yaml
    ka busybox-notexist.yaml
    k logs busybox-notexist
    kd pod busybox-notexist
    k delete pod busybox-notexist --force --grace-period=0

8. Get CPU/memory utilization for nodes (metrics-server must be running)
    k top nodes