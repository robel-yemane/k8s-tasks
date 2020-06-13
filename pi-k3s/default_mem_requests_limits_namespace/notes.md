

If a LimitRange is created in a namespace that specifies default limit and/or default request one of these things happens:

1. Container doesn't specify request and memory limit.
=> Container is given a default memory request and default memory limit as
specified in the LimitRange.

2. Container specifies its limit, but not its request.
=> Container's memory request is set to match its memory limit. (The default
memory request doesn't get assigned).

3. Container specifies its request but not it's limit.
=> Container's memory request is set to the value specified in the Container's
configuration file, but the memory limit is set to the default for the
namespace.

--
Motivation for default memory limits and requests

if a namespace has a resource quota, it helps to have a default value for memory
limits. Some of the restrictions imposed on a namespace by a resource quota
are:

1. Every container that runs in the namespace must have its own memory limit.
2. The total amount of memory used by all Containers in the namespace must not
exceed a specified limit.

If a container does not specify its own memory limit, it is given the default
limit, and then it can be allowed to run in a namespace that is restricted by a
quota.

