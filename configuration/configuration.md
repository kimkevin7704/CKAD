1. Create a configmap named config with values foo=lala,foo2=lolo
    k create cm config --from-literal=foo=lala --from-literal=foo2=lolo --dry-run=client -oyaml > configmap.yaml
    ka configmap.yaml

2. dispaly its values
    kd cm config

3. create and display a configmap from a file
    echo -e "foo3=lili\nfoo4=lele" > config.txt
    k create cm config --from-file=config.txt
    kd cm config

4. create and display a configmap from a .env file
    echo -e "var1=val1\n# this is a comment\n\nvar2=val2\n#anothercomment" > config.env
    k create cm config --from-env-file=config.env
    kd cm config

5. create and display a configmap from a file, giving the key 'special'
    echo -e "var3=val3\nvar4=val4" > config4.txt
    k create cm config --from-file=special=config4.txt

6. Create a configMap called 'options' with the value var5=val5. Create a new nginx pod that loads the value from variable 'var5' in an env variable called 'option'
    k create cm options --from-literal=var5=val5
    k run nginx --image=nginx --dry-run=client -oyaml > nginx.yaml
    add env in pod.spec.containers
        - name: option
          valueFrom:
            configMapKeyRef:
              name: options
              key: var5

7. Create a configMap 'anotherone' with values 'var6=val6', 'var7=val7'. Load this configMap as env variables into a new nginx pod
    k create cm anotherone --from-literal=var6=val6 --from-literal=var7=val7
    k run nginx2 --image=nginx --dry-run=client -oyaml > nginx2.yaml
    vi nginx2.yaml
    add envFrom to pod.spec.containers

8. Create a configMap 'cmvolume' with values 'var8=val8', 'var9=val9'. Load this as a volume inside an nginx pod on path '/etc/lala'. Create the pod and 'ls' into the '/etc/lala' directory.
    k create cm cmvolume --from-literal=var8=val8 --from-literal=var9=val9
    k run nginx3 --image=nginx --dry-run=client -oyaml > nginx3.yaml
    add volumeMounts in pod.spec.containers
        - name: cmvolume
          mountPath: /etc/lala
    add volumes in pod.spec
        - name: cmvolume
          configMap:
            name: cmvolume

9. Create the YAML for an nginx pod that runs with the user ID 101. No need to create the pod
    k run nginx-sc --image=nginx --dry-run=client -oyaml > nginx-sc.yaml
    add securityContext to pod.spec
        runAsUser: 101

10. Create the YAML for an nginx pod that has the capabilities "NET_ADMIN", "SYS_TIME" added to its single container
    k run nginx-sc2 --image=nginx --dry-run=client -oyaml > nginx-sc2.yaml
    add securityContext to pod.spec.containers
        capabilities:
            add: ["NET_ADMIN", "SYS_TIME"]

11. Create an nginx pod with requests cpu=100m,memory=256Mi and limits cpu=200m,memory=512Mi
    k run nginx-rl --image=nginx --dry-run=client -oyaml > nginx-rl.yaml
    add in pod.spec.containers.resources
        requests:
            cpu: 100m
            memory: 256Mi
        limits:
            cpu: 200m
            memory: 512Mi

12. Create a secret called mysecret with the values password=mypass
    k create secret generic mysecret --from-literal=password=mypass

13. Create a secret called mysecret2 that gets key/value from a file
    echo -n admin > username
    k create secret generic mysecret2 --from-file=username

14. See all the service accounts of the cluster in all namespaces
    k get sa --all-namespaces

15. Create a new serviceaccount called 'myuser'
    k create sa myuser

16. Create an nginx pod that uses 'myuser' as a service account
    k run nginx-sa --image=nginx --dry-run=client -oyaml > nginx-sa.yaml
    add serviceAccount: myuser in pod.spec
    ka nginx-sa.yaml
