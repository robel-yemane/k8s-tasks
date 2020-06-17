How to set quotas (ResourceQuota) for the total amount of memory and CPU that can be used by
all Containers running in a namespace. 

Name:            mem-cpu-demo
Namespace:       quota-mem-cpu-example
Resource         Used  Hard
--------         ----  ----
limits.cpu       0     2
limits.memory    0     2Gi
requests.cpu     0     1
requests.memory  0     1Gi

The above rq places these requirements on the namespace it has been deployed:
* every container must have a mem request, mem limit, cpu request and cpu
 limit.
* The memory request total for all Containers must not exceed 1GiB.
* The memory limit total for all Containers must not exceed 2GiB.
* The CPU request total for all Containers must not exceed 1 cpu.
* The CPU limit total for all Containers must not exceed 2 cpu.

Cases:

1. Deployed a pod with these resource limits
    Limits:
      cpu:     800m
      memory:  800Mi
    Requests:
      cpu:        400m
      memory:     600Mi

=> k describe resourcequotas mem-cpu-demo

Name:            mem-cpu-demo
Namespace:       quota-mem-cpu-example
Resource         Used   Hard
--------         ----   ----
limits.cpu       800m   2
limits.memory    800Mi  2Gi
requests.cpu     400m   1
requests.memory  600Mi  1Gi

2. Attempt to create another pod with these resource limits:
    resources:
      limits:
        memory: "1Gi"
        cpu: "800m"
      requests:
        memory: "700Mi"
        cpu: "400m"

=> k apply -f quota-mem-cpu-pod-2.yaml
Error from server (Forbidden): error when creating "quota-mem-cpu-pod-2.yaml": pods "quota-mem-cpu-demo-2" is forbidden: exceeded quota: mem-cpu-demo, requested: requests.memory=700Mi, used: requests.memory=600Mi, limited: requests.memory=1Gi

Pod doesn't get created as the sum of the used memory request and the new
memory request exceeds the mem request quota.

You can use a ResourceQuota to restrict the memory request total for all
Containers running in a namespace. You can also restrict the totals for mem
limit, cpu request, and cpu limit.

To restrict individual Containers, instead of totals for all Containers, use
`LimitRange`
