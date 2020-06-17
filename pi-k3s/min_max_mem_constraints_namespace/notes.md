This page shows how to set minimum and maximum values for memory used by Containers running in a namespace.
 You specify minimum and maximum memory values in a LimitRange object. If a Pod does not meet the constraints
 imposed by the LimitRange, it cannot be created in the namespace.

with a LimitRange that specifies max and min memory set, the following can
happen:

1. If a LimitRange specifies only a max and min memory, default values are
autmatically created that match the * maximum * memory specified.

Kubernetes then performs these steps:

 * if the container does not specify its own memory request and limits, the default
memory request and limit are assigned to the container.
 * verify that the container has a memory request that is greater than or equal to
  the min memory specified. 

2. When a Pod is created that exceeds the maximum memory constraints:

 * The Pod does not get created, because the container specifies a memory limit that
  is too large.

3. When a Pod is created that is below the minimum memory request:

 * The Pod does not get created, becase the container specifies a memory request that
  is smaller thatn the minimum allowed.

4. When a Pod that doesn't specify any mem request or limit is created:

 * The Pod's container has a memory request and limit that matches the defaults in
   the LimitRange. 
Recap: A Pod to be scheduled in a Node, has to have mem limits <= mem
limits(LimitRange) && mem requests >= mem requests(LimitRange)
maximum memory specifed and should have greater memory that the minim 
--
Note: The maximum and minimum memory constraints imposed on a namespace by a
LimitRange are enforced only when a Pod is created or updated. If you change the
LimitRange, it does not affect Pods that were created previously.



