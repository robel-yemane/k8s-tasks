limitrange applied in cluster:

Name:       cpu-min-max-demo-lr
Namespace:  constraints-cpu-example
Type        Resource  Min   Max   Default Request  Default Limit  Max Limit/Request Ratio
----        --------  ---   ---   ---------------  -------------  -----------------------
Container   cpu       200m  800m  800m             800m           -

Now whenever a Container is created in the constraints-cpu-example namespace, Kubernetes performs these steps:

    If the Container does not specify its own CPU request and limit, assign the default CPU request and limit to the Container.

    Verify that the Container specifies a CPU request that is greater than or equal to 200 millicpu.

    Verify that the Container specifies a CPU limit that is less than or equal to 800 millicpu.


----

Note:
Enforcement of minimum and maximum CPU constraints
The maximum and minimum CPU constraints imposed on a namespace by a LimitRange are enforced only when a
Pod is created or updated. If you change the LimitRange, it does not affect Pods that were created previously.
