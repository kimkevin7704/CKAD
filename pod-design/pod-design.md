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

18. Verify that something's wrong with the rollout
    k rollout status deploy nginx

19. return deployment to nginx:1.19.8
    k rollout undo deploy nginx --to-revision=9
    kd deploy nginx
    k rollout status deploy nginx

20. check the details of the fourth revision
    k rollout history deploy nginx --revision=4

21. scale the deployment to 5 replicas
    k scale deploy/nginx --replicas=5
    k rollout status deploy/nginx
    kd deploy/nginx

22. autoscale the deployment, pods between 5 and 10, targetting cpu utilization at 80%
    k autoscale deploy/nginx --min=5 --max=10 --cpu-percent=80
    kd hpa nginx

23. pause the rollout of the deployment
    k rollout pause deploy/nginx

24. update the image to 1.19.9 and check nothing is going on due to paused rollout
    k set image deploy/nginx nginx=nginx:1.19.9
    k rollout status deploy/nginx

25. resume the rollout and check that nginx:19.9.9 image has been applied
    k rollout resume deploy/nginx
    k rollout status deploy/nginx

26. delete deployment and horizontal pod autoscaler
    k delete deploy nginx
    k delete hpa nginx

27. implement canary deployment by running two instances of nginx marked as version=v1 and version=v2 so that the load is balanced at 75-25% ratio
    k create deploy nginx1 --image=nginx --replicas=3 --dry-run=client -oyaml > deploy-1.yaml
    k create deploy nginx2 --image=nginx --replicas=1 --dry-run=client -oyaml > deploy-2.yaml
    edit deploy.spec.selector.matchLabels
    edit deploy.spec.template.metadata.labels

28. Create a job named pi with image perl:5.34 that runs the command with arguments "perl -Mbignum=bpi -wle 'print bpi(2000)'"
    k create job pi --image=perl:5.34 -- perl -Mbignum=bpi -wle 'print bpi(2000)'

29. Wait till it's done, get the output
    k logs job/pi

30. Create a job with the image busybox that executes the command 'echo hello;sleep 30;echo world'
    k create job busybox --image=busybox -- /bin/sh -c "echo hello;sleep 30;echo world"
    k get pod
    k logs {pod} -f  (-f for follow)

31. Follow the logs for the pod (you'll wait for 30 seconds)
    k get pod
    k logs {pod} -f  (-f for follow)

32. See the status of the job, describe it and see the logs
    k get jobs
    kd jobs/busybox
    k logs jobs/busybox

33. Delete the job
    k delete job busybox

34. Create a job but ensure that it will be automatically terminated by kubernetes if it takes more than 30 seconds to execute
    k create job busybox --image=busybox --dry-run=client -oyaml > busybox-auto-terminate.yaml
    in busybox-auto-terminate add activeDeadlineSeconds in job.spec

35. Create the same job, make it run 5 times, one after the other. Verify its status and delete it
    same as above but add completions in job.spec

36. Create the same job, but make it run 5 parallel times
    same as above but add parallelism: 5 in job.spec

37. Create a cron job with image busybox that runs on a schedule of "*/1 * * * *" and writes 'date; echo Hello from the Kubernetes cluster' to standard output
    k create cj busybox --image=busybox --schedule="*/1 * * * *" -- /bin/sh -c 'date; echo Hello from the Kubernetes cluster'

38. See its logs and delete it
    k get job -w
    k get pod --show-labels (find which parent job started up the pod)
    k logs {pod}

29. Create the same cron job again, and watch the status. Once it ran, check which job ran by the created cron job. Check the log, and delete the cron job
    k get job -w
    k get pod --show-labels (find which parent job started up the pod)
    k logs {pod}

30. Create a cron job with image busybox that runs every minute and writes 'date; echo Hello from the Kubernetes cluster' to standard output. The cron job should be terminated if it takes more than 17 seconds to start execution after its scheduled time (i.e. the job missed its scheduled time).

31. Create a cron job with image busybox that runs every minute and writes 'date; echo Hello from the Kubernetes cluster' to standard output. The cron job should be terminated if it successfully starts but takes more than 12 seconds to complete execution.

