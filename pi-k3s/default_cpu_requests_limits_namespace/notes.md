If a Container is created in a namespace that has a default CPU limit, and the Container does not specify its own CPU limit, 
then the Container is assigned the default CPU limit. Kubernetes assigns a default CPU request under certain conditions listed below.

1.Container doesn't specify its own values for CPU request and CPU limit.
=>Container is given a default CPU request and limit as specifed in the limitrange of the namespace.

2.Container specifies limit but not request.
=>Container is given a matching request (to the limit specified in the
container config).
Container does not get assigned the default CPU request

3.Container specifies request but not limit.
=>Container is assigned the request specified, limit is given from the
limitrange specified on the namespace.

--

Motivation for default CPU limits and requests

If your namespace has a resource quota, it is helpful to have a default value
in place for CPU limit. Here are two of the restrictions that a resource quota
imposes on a namespace.

    - Every container that runs in the namespace must have its own CPU limit.
    - The total amount of CPU used by all Containers in the namespace must not
      exceed a specified limit.

If a Container does not specify its own CPU limit, it is given the default
limit, and then it can be allowed to run in a namespace that is restricted by a
quota.

--
ps: if a namespace does not have a resourcequota specified and only
limitranges. The container will still be allowed to run even though it
specifies (in its configuration) a higher (than specified in limitranges) cpu
request/limit.

Therefore, limitranges are applied only when the container does not specify any
in its own config.

NB: While your Kubernetes cluster might work fine without setting resource
requests and limits, you will start running into stability issues as your teams and projects grow.
Adding requests and limits to your Pods and Namespaces only takes a little
extra effort, and can save you from running into many headaches down the line!
