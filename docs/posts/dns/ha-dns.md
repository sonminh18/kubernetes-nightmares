# Using HA for DNS in Kubernetes Cluster

---


**DNS in Kubernetes Cluster**

![Image.png](https://miro.medium.com/v2/resize:fit:1400/1*Xgv-uxWEqUC9ptuTFSHuPQ.png)

DNS is an essential component of any network infrastructure, providing resolution for hostnames to IP addresses. 

In a Kubernetes cluster, DNS plays a critical role in enabling communication between pods and services, as well as with external resources. 

**CoreDNS**

CoreDNS is one of the most popular open-source for DNS service in Kubernetes and is responsible for handling the resolution of service records. 

CoreDNS acts as a deployment set in the cluster and listens for DNS requests from other pods. 
When a pod makes a request for a dns record, CoreDNS uses the Kubernetes API server to look up the IP address associated with the requested service. The API server then returns the IP address to CoreDNS, which in turn returns it to the requesting pod.

**NodeLocalDNS**

NodeLocalDNS is a new feature in Kubernetes that acts a DNS cache in the cluster. NodeLocalDNS is also used to resolve external DNS records, such as the IP addresses of external services.

![Image.png](https://raw.githubusercontent.com/sonminh18/kubernetes-nightmares/main/docs/assets/img/posts/nodelocaldns.png)

NodeLocalDNS was built based on CoreDNS, but it only keeps cache plugin of CoreDNS and was designed to run as a daemon set, not like CoreDNS.

NodeLocalDNS can help to improve DNS query performance. For example, assumpt that you're setup cluster by RKE or KubeKey with default configuration. It usually deploys a Calico as network plugin + kube-proxy and CoreDNS. The latency of DNS query will be increased when the number of pods are increasing. This is because kube-proxy is implementing with `iptables` and when a pod make a DNS request to CoreDNS, the traffic will be sent out via `iptables` and it has some limitations.

https://kubernetes.io/docs/tasks/administer-cluster/nodelocaldns/#motivation
https://github.com/kubernetes/kubernetes/issues/56903

But in the other hand, NodeLocalDNS also has issue if you're using a `headless` service.

**What if DNS component is crashed??**

As mentioned above, DNS is one of core component of Kubernetes Cluster, so when it is crashed, it means that all pods in cluster can not resolve DNS, even if pod calls a request to another pod inside cluster. That's really a nightmare if you're running a production.

Both CoreDNS and NodeLocalDNS also can run independence as a DNS server, but if we combined both of them, then we have a HA solution for DNS service inside cluster.

![Image.png](https://raw.githubusercontent.com/sonminh18/kubernetes-nightmares/main/docs/assets/img/posts/ha-dns.png)