# Using HA for DNS in Kubernetes Cluster

---


DNS is an essential component of any network infrastructure, providing resolution for hostnames to IP addresses. In a Kubernetes cluster, DNS plays a critical role in enabling communication between pods and services, as well as with external resources.
Let's see if we're running a production with Kubernetes, and DNS component is crashed, then all of pods in cluster can not resolve DNS. That is really a nightmare.


**CoreDNS**

- CoreDNS is one of the most popular DNS servers in Kubernetes and is responsible for handling the resolution of service records. CoreDNS runs as a deployment set in the cluster and listens for DNS requests from other pods. 
- When a pod makes a request for a service record, CoreDNS uses the Kubernetes API server to look up the IP address associated with the requested service. The API server then returns the IP address to CoreDNS, which in turn returns it to the requesting pod.

**NodeLocalDNS**

- NodeLocalDNS is a new feature in Kubernetes that enables you to run a daemonSet in the cluster. NodeLocalDNS is also used to resolve external DNS records, such as the IP addresses of external services. 
- NodeLocalDNS was built based on CoreDNS, but it only keeps cache plugin and remove other features. 
- NodeLocalDNS can help to improve DNS query performance. For example, assumpt that you're setup cluster by RKE or KubeKey with default configuration. It usually deploys a Calico as network plugin + kube-proxy and CoreDNS. The latency of DNS query will be increased when the number of pods are increasing. This is because kube-proxy is implementing with `iptables` and when a pod make a DNS request to CoreDNS, the traffic will be sent out via `iptables` and it has some limitations.

https://kubernetes.io/docs/tasks/administer-cluster/nodelocaldns/#motivation
https://github.com/kubernetes/kubernetes/issues/56903

But in the other hand, NodeLocalDNS also has issue if you're using a `headless` service.

## Combine CoreDNS and NodeLocalDNS, why don't we?

![Image.png](https://raw.githubusercontent.com/sonminh18/kubernetes-nightmares/main/docs/assets/img/posts/HA-DNS.png)