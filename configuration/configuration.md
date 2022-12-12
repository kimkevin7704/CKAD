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