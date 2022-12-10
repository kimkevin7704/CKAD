![](https://gaforgithub.azurewebsites.net/api?repo=CKAD-exercises/pod_design&empty)
# Pod design (20%)

[Labels And Annotations](#labels-and-annotations)

[Deployments](#deployments)

[Jobs](#jobs)

[Cron Jobs](#cron-jobs)

## Labels and annotations
kubernetes.io > Documentation > Concepts > Overview > Working with Kubernetes Objects > [Labels and Selectors](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#label-selectors)

1. create 3 pods with names nginx1,nginx2,nginx3. All of them should have the label app=v1
    k run nginx1 --image=nginx --labels=app=v1
    k run nginx2 --image=nginx --labels=app=v1
    k run nginx3 --image=nginx --labels=app=v1

2. Show all labels of the pods
    k get pods --show-labels

3. Change the labels of pod 'nginx2' to be app=v2
    k label --overwrite pod nginx2 app=v2

4. Get the label 'app' for the pods (show a column with APP labels)
    k get pod -L app        -L for label column

5. Get only the 'app=v2' pods
    k get pod -l app=v2     -l for selector

6. Add a new label tier=web to all pods having 'app=v2' or 'app=v1' labels
    k label pod -l app=v1 tier=web
    k label pod -l app=v2 tier=web
    OR
    k label pod -l "app in(v1,v2)" tier=web

7. Add an annotation 'owner: marketing' to all pods having 'app=v2' label
    k annotate pod -l app=v2 owner=marketing

8. Remove the 'app' label from the pods we created before
    k label pod -l "app in(v1,v2)" app-

9. Create a pod that will be deployed to a Node that has the label 'accelerator=nvidia-tesla-p100'
    k label node minikube accelerator=nvidia-tesla-p100
    k run nginx --image=nginx --dry-run=client -oyaml > nginx.yaml
    add nodeSelector at pod.spec.nodeSelector
    nodeSelector:
      accelerator: nvidia-tesla-p100

10. Annotate pods nginx1, nginx2, nginx3 with "description='my description'" value
    pod-design % k annotate pod -l tier "description='my description'"

11. Check the annotations for pod nginx1
    kd pod nginx1 | grep annotations -i     # -i to ignore casing

12. Remove the annotations for these three pods
    k annotate pod nginx{1..3} description-

13. Remove these pods to have a clean state in your cluster
    k delete pod nginx{1..3}

14. Create a deployment with image nginx:1.18.0, called nginx, having 2 replicas, defining port 80 as the port that this container exposes (don't create a service for this deployment)
    k create deploy nginx --image=nginx:1.18.0 --replicas=2 --port=80 --dry-run=client -oyaml > deploy.yaml
    ka deploy.yaml

15. Get the YAML for one of the pods
    k get pod nginx-578b7694b4-jzqff -oyaml > pod.yaml

16. Check how the deployment rollout is going
    k rollout status deploy/nginx

17. Update the nginx image to nginx:1.19.8
    k edit deploy nginx
