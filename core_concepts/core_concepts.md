kubernetes.io > Documentation > Reference > kubectl CLI > [kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)

kubernetes.io > Documentation > Tasks > Monitoring, Logging, and Debugging > [Get a Shell to a Running Container](https://kubernetes.io/docs/tasks/debug-application-cluster/get-shell-running-container/)

kubernetes.io > Documentation > Tasks > Access Applications in a Cluster > [Configure Access to Multiple Clusters](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/)

kubernetes.io > Documentation > Tasks > Access Applications in a Cluster > [Accessing Clusters](https://kubernetes.io/docs/tasks/access-application-cluster/access-cluster/) using API

kubernetes.io > Documentation > Tasks > Access Applications in a Cluster > [Use Port Forwarding to Access Applications in a Cluster](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/)

1. Create a namespace called 'mynamespace' and a pod with image nginx called nginx on this namespace
    k create ns mynamespace
    k run nginx --image=nginx --namespace=mynamespace

2. Create the pod that was just described using YAML
    k run nginx --image=nginx --namespace=mynamespace --dry-run=client -oyaml > nginx.yaml
    ka nginx.yaml

3. Create a busybox pod (using kubectl command) that runs the command "env". Run it and see the output
    k run busybox --image=busybox --dry-run=client -oyaml --command -- env > envpod.yaml

4. Get the YAML for a new namespace called 'myns' without creating it
    k create namespace myns --dry-run=client -oyaml > myns.yaml

5. Get YAML for ResourceQuota called 'myrq' with hard limits of 1 CPU, 1G memory and 2 pods without creating it
    k create quota myrq --hard=cpu=1,memory=1G,pods=2 --dry-run=client -oyaml > myrq.yaml

6. Get pods on all namespaces
    k get pod --all-namespaces

7. Create a pod with image nginx called nginx and expose traffic on port 80
    k run nginx --image=nginx --port=80

8. Chage pod's image to nginx:1.7.1 Observe that the container will be restarted as soon as the image gets pulled
    k edit pod nginx --> edit image
    k set image pod/nginx nginx=nginx:1.7.1
    k get pod nginx -w # watch it

9. Get nginx pod's ip created in previous step, use a temp busybox image to wget its '/'
    k get pod -o wide
    k run temp --image=busybox --rm -it -- wget -O- 172.17.0.4:80
    k logs temp

10. get pod's YAML
    k get pod nginx -oyaml > nginx-example.yaml

11. Get information about the pod, including details about potential issues
    kd pod nginx

12. Get pod logs
    k logs nginx

13. If pod crashed and restarted, get logs about the previous instance
    k logs nginx -p

14. Execute a simple shell on the nginx pod
    k exec -it nginx -- echo "hello"

15. create a busybox pod that echoes 'hello worlk' and then exits
    k run hello --image=busybox --command -it -- echo "hello world"

16. same as 15 but delete the pod automatically after completion
    k run hello --image=busybox --rm --restart=Never --command -it -- echo "hello world"

17. create an nginx pod and set an env value as 'var1=val1'. Check the env value existence within the pod
    k run nginx-env --image=nginx --env=var1=val1 --dry-run=client -oyaml > nginx-env.yaml
    k exec -it nginx-env -- /bin/sh
    printenv
