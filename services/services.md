1. Create a pod with image nginx called nginx and expose its port 80
    k create deploy nginx --image=nginx --port=80 --expose
 
2. Confirm that ClusterIP has been created. Also check endpoints
    k get svc
    k get ep

3. Get service's ClusterIP, create a temp busybox pod and 'hit' that IP with wget
    k run busybox --image=busybox --rm -it -- sh
    wget -O- {IP}:80
 
4. Convert the ClusterIP to NodePort for the same service and find the NodePort port. Hit service using Node's IP. Delete the service and the pod at the end.
    k edit svc nginx
    replace type: ClusterIP --> NodePort
    k get nodes -o wide
    k run busybox --rm -it --image=busybox -- sh
    wget -O- {nodeIP}:{nodePort}
 
5. Create a deployment called foo using image 'dgkanatsios/simpleapp' (a simple server that returns hostname) and 3 replicas. Label it as 'app=foo'. Declare that containers in this pod will accept traffic on port 8080 (do NOT create a service yet)
    k create deploy foo --image=dgkanatsios/simpleapp --replicas=3 --port=8080 --dry-run=client -oyaml > foo.yaml
 
6. Get the pod IPs. Create a temp busybox pod and try hitting them on port 8080
    k get pod -o wide
    k run temp --image=busybox --rm -it -- sh
    wget -O- {podIP}:{port}
 
7. Create a service that exposes the deployment on port 6262. Verify its existence, check the endpoints
    k expose deploy foo --port=6262 --target-port=8080
    k get ep
 
8. Create a temp busybox pod and connect via wget to foo service. Verify that each time there's a different hostname returned. Delete deployment and services to cleanup the cluster
    k run temp --image=busybox --rm -it -- sh
    wget -O- {svcIP}:{svcPort}
 
9. Create an nginx deployment of 2 replicas, expose it via a ClusterIP service on port 80. Create a NetworkPolicy so that only pods with labels 'access: granted' can access the deployment and apply it
kubernetes.io > Documentation > Concepts > Services, Load Balancing, and Networking > Network Policies

10. Note that network policies may not be enforced by default, depending on your k8s implementation. E.g. Azure AKS by default won't have policy enforcement, the cluster must be created with an explicit support for netpol https://docs.microsoft.com/en-us/azure/aks/use-network-policies#overview-of-network-policy
