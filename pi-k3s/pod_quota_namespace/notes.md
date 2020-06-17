Name:       pod-demo
Namespace:  quota-pod-example
Resource    Used  Hard
--------    ----  ----
pods        0     2


The above resource quota is specifying 2 pods quota for the ns.

When  a deployment with 3 replicas has been applied, only 2/3 pods have been
created.
 
Output of `k get events` below shows the cause of the missing pod.

31s         Warning   FailedCreate        replicaset/pod-quota-demo-56d6f96f68   (combined from similar events): Error creating: pods "pod-quota-demo-56d6f96f68-xwvqs" is forbidden: exceeded quota: pod-demo, requested: pods=1, used: pods=2, limited: pods=2
