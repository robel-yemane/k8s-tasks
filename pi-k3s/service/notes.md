kubernetes gives Pods their own IP addresses and a single DNS name for a set of
Pods, and can load-balance across them.

pods are mortal and they don't get resurrected. Other pods (if part of a deployment)
get created.
Service is a layer of abstraction that provides means of connection to/with a
(set of) pod(s).

Service Discovery
=================

cloud-native service discovery
------------------------------
you can use the API server for querying the Endpoints (resources which are updated
whenever the set of Pods in a service changes) or

non-cloud-native approach
--------------------------
for non-cloud-native apps k8s offers a network port or loadbalancer in between
your apps and the backend pods.

```
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: MyApp
  ports:
    - protocol: TCP
      port: 80	
      targetPort: 9376
```
(the corresponding endpoint automatically created is as below)
```
apiVersion: v1
kind: Endpoints
metadata:
  annotations:
    endpoints.kubernetes.io/last-change-trigger-time: "2020-07-01T14:52:23+01:00"
  creationTimestamp: "2020-07-01T13:52:23Z"
  name: my-service
  namespace: default
  resourceVersion: "287245"
  selfLink: /api/v1/namespaces/default/endpoints/my-service
  uid: 81c08941-d401-468d-bcf8-c72eede2a761
```
^ This specification creates a new Service object named "my-service", which
targets TCP port 9376 on any Pod with the `app=Myapp` label. K8s assigns this
Service an IP address  (called 'cluster IP') which is used by the Service
proxies.

- Default protocol for services is TCP, but any other supported (tcp, udp,
  proxy protocol, sctp) protocol can be used.
  K8s supports multiple port definitions on a Service object. Each port
  definition can have the same `protocol` or a different one.


service without selectors
------------------------- 
services typically abstract access to k8s Pods. They can also abstract other
kinds of backends. e.g.

    - external db cluster in prod, in test env another database (test/mock)
    - point a service to another service in a different namespace or other
      cluster.
    - during migration to k8s, if only a portion of workload/backend is run in
      k8s (testing purposes).
in any of the above scenario define a service _without_ a Pod selector.
e.g.

```
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9376
```
since this Service has no selector, the corresponding Endpoint object is _not_
created automatically. You can manually map the Service to the network address
and port where it's running, by adding an Endpoint object manually.
Endpoint 

```
apiVersion: v1
kind: Endpoints
metadata:
  name: my-service
subsets:
  - addresses:
     - ip: 192.0.2.42
    ports:
      - port: 9376
```
^ the endpoint ip must not be a loopback or link-local. It cannot be the
cluster IPs of other k8s services (b/c kube-proxy doesn't support virtual IPs
as a destination).


=============================================================================
