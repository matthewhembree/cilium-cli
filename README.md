# Next-Gen Cilium CLI (Experimental)

[![Go](https://github.com/cilium/cilium-cli/workflows/Go/badge.svg)](https://github.com/cilium/cilium-cli/actions?query=workflow%3AGo)
[![Kind](https://github.com/cilium/cilium-cli/workflows/Kind/badge.svg)](https://github.com/cilium/cilium-cli/actions?query=workflow%3AKind)
[![EKS (ENI)](https://github.com/cilium/cilium-cli/actions/workflows/eks.yaml/badge.svg)](https://github.com/cilium/cilium-cli/actions/workflows/eks.yaml)
[![EKS (tunnel)](https://github.com/cilium/cilium-cli/actions/workflows/eks-tunnel.yaml/badge.svg)](https://github.com/cilium/cilium-cli/actions/workflows/eks-tunnel.yaml)
[![GKE](https://github.com/cilium/cilium-cli/workflows/GKE/badge.svg)](https://github.com/cilium/cilium-cli/actions?query=workflow%3AGKE)
[![AKS](https://github.com/cilium/cilium-cli/workflows/AKS/badge.svg)](https://github.com/cilium/cilium-cli/actions?query=workflow%3AAKS)
[![Multicluster](https://github.com/cilium/cilium-cli/workflows/Multicluster/badge.svg)](https://github.com/cilium/cilium-cli/actions?query=workflow%3AMulticluster)
[![External Workloads](https://github.com/cilium/cilium-cli/actions/workflows/externalworkloads.yaml/badge.svg)](https://github.com/cilium/cilium-cli/actions/workflows/externalworkloads.yaml)

## Installation

To build and install, use the `install` target:

```console
make install
```

You may set the `BINDIR` environment variable to install the binary in a
specific location instead of `/usr/local/bin`, e.g.

```
BINDIR=~/.local/bin make install
```

## Capabilities

### Install Cilium

To install Cilium while automatically detected:

    cilium install
    🔮 Auto-detected Kubernetes kind: minikube
    ✨ Running "minikube" validation checks
    ✅ Detected minikube version "1.5.2"
    ℹ️  Cilium version not set, using default version "v1.9.1"
    🔮 Auto-detected cluster name: minikube
    🔮 Auto-detected datapath mode: tunnel
    🔑 Found existing CA in secret cilium-ca
    🔑 Generating certificates for Hubble...
    🚀 Creating service accounts...
    🚀 Creating cluster roles...
    🚀 Creating ConfigMap...
    🚀 Creating agent DaemonSet...
    🚀 Creating operator Deployment...

#### Supported Environments

 - [x] minikube
 - [x] kind
 - [x] EKS
 - [x] self-managed
 - [x] GKE
 - [x] AKS
 - [ ] k3s
 - [ ] Rancher

### Cluster Context Management

    cilium context
    Context: minikube
    Cluster: minikube
    Auth: minikube
    Host: https://192.168.64.25:8443
    TLS server name:
    CA path: /Users/tgraf/.minikube/ca.crt

### Hubble

    cilium hubble enable
    🔑 Generating certificates for Relay...
    ✨ Deploying Relay...

### Status

    cilium status
        /¯¯\
     /¯¯\__/¯¯\    Cilium:      OK
     \__/¯¯\__/    Operator:    OK
     /¯¯\__/¯¯\    Hubble:      OK
     \__/¯¯\__/
        \__/
    DaemonSet         cilium             Desired: 1, Ready: 1/1, Available: 1/1
    Deployment        cilium-operator    Desired: 1, Ready: 1/1, Available: 1/1
    Deployment        hubble-relay       Desired: 1, Ready: 1/1, Available: 1/1
    Containers:       cilium             Running: 1
                      cilium-operator    Running: 1
                      hubble-relay       Running: 1
    Image versions    cilium             quay.io/cilium/cilium:v1.9.1: 1
                      cilium-operator    quay.io/cilium/operator-generic:v1.9.1: 1
                      hubble-relay       quay.io/cilium/hubble-relay:v1.9.1: 1

### Connectivity Check

    cilium connectivity test --single-node
    ⌛ Waiting for deployments to become ready
    🔭 Enabling Hubble telescope...
    ⚠️  Unable to contact Hubble Relay: rpc error: code = Unavailable desc = connection error: desc = "transport: Error while dialing dial tcp [::1]:4245: connect: connection refused"
    ⚠️  Did you enable and expose Hubble + Relay?
    ℹ️  You can export Relay with a port-forward: kubectl port-forward -n kube-system deployment/hubble-relay 4245:4245
    ℹ️  Disabling Hubble telescope and flow validation...
    -------------------------------------------------------------------------------------------
    🔌 Validating from pod cilium-test/client-9f579495f-b2pcq to pod cilium-test/echo-same-node-7f877bbf9-p2xg8...
    -------------------------------------------------------------------------------------------
    ✅ client pod client-9f579495f-b2pcq was able to communicate with echo pod echo-same-node-7f877bbf9-p2xg8 (10.0.0.166)
    -------------------------------------------------------------------------------------------
    🔌 Validating from pod cilium-test/client-9f579495f-b2pcq to outside of cluster...
    -------------------------------------------------------------------------------------------
    ✅ client pod client-9f579495f-b2pcq was able to communicate with cilium.io
    -------------------------------------------------------------------------------------------
    🔌 Validating from pod cilium-test/client-9f579495f-b2pcq to local host...
    -------------------------------------------------------------------------------------------
    ✅ client pod client-9f579495f-b2pcq was able to communicate with local host
    -------------------------------------------------------------------------------------------
    🔌 Validating from pod cilium-test/client-9f579495f-b2pcq to service echo-same-node...
    -------------------------------------------------------------------------------------------
    ✅ client pod client-9f579495f-b2pcq was able to communicate with service echo-same-node

#### With Flow Validation

    cilium hubble port-forward&
    cilium connectivity test --single-node
    ⌛ Waiting for deployments to become ready
    🔭 Enabling Hubble telescope...
    Handling connection for 4245
    ℹ️  Hubble is OK, flows: 405/4096
    -------------------------------------------------------------------------------------------
    🔌 Validating from pod cilium-test/client-9f579495f-b2pcq to pod cilium-test/echo-same-node-7f877bbf9-p2xg8...
    -------------------------------------------------------------------------------------------
    📄 Flow logs of pod cilium-test/client-9f579495f-b2pcq:
    Jan  6 13:41:17.739: 10.0.0.11:43876 -> 10.0.0.166:8080 to-endpoint FORWARDED (TCP Flags: SYN)
    Jan  6 13:41:17.739: 10.0.0.166:8080 -> 10.0.0.11:43876 to-endpoint FORWARDED (TCP Flags: SYN, ACK)
    Jan  6 13:41:17.739: 10.0.0.11:43876 -> 10.0.0.166:8080 to-endpoint FORWARDED (TCP Flags: ACK)
    Jan  6 13:41:17.739: 10.0.0.11:43876 -> 10.0.0.166:8080 to-endpoint FORWARDED (TCP Flags: ACK, PSH)
    Jan  6 13:41:17.755: 10.0.0.166:8080 -> 10.0.0.11:43876 to-endpoint FORWARDED (TCP Flags: ACK, PSH)
    Jan  6 13:41:17.756: 10.0.0.11:43876 -> 10.0.0.166:8080 to-endpoint FORWARDED (TCP Flags: ACK, FIN)
    Jan  6 13:41:17.757: 10.0.0.166:8080 -> 10.0.0.11:43876 to-endpoint FORWARDED (TCP Flags: ACK, FIN)
    Jan  6 13:41:17.757: 10.0.0.11:43876 -> 10.0.0.166:8080 to-endpoint FORWARDED (TCP Flags: ACK)
    📄 Flow logs of pod cilium-test/echo-same-node-7f877bbf9-p2xg8:
    Jan  6 13:41:17.739: 10.0.0.11:43876 -> 10.0.0.166:8080 to-endpoint FORWARDED (TCP Flags: SYN)
    Jan  6 13:41:17.739: 10.0.0.166:8080 -> 10.0.0.11:43876 to-endpoint FORWARDED (TCP Flags: SYN, ACK)
    Jan  6 13:41:17.739: 10.0.0.11:43876 -> 10.0.0.166:8080 to-endpoint FORWARDED (TCP Flags: ACK)
    Jan  6 13:41:17.739: 10.0.0.11:43876 -> 10.0.0.166:8080 to-endpoint FORWARDED (TCP Flags: ACK, PSH)
    Jan  6 13:41:17.755: 10.0.0.166:8080 -> 10.0.0.11:43876 to-endpoint FORWARDED (TCP Flags: ACK, PSH)
    Jan  6 13:41:17.756: 10.0.0.11:43876 -> 10.0.0.166:8080 to-endpoint FORWARDED (TCP Flags: ACK, FIN)
    Jan  6 13:41:17.757: 10.0.0.166:8080 -> 10.0.0.11:43876 to-endpoint FORWARDED (TCP Flags: ACK, FIN)
    Jan  6 13:41:17.757: 10.0.0.11:43876 -> 10.0.0.166:8080 to-endpoint FORWARDED (TCP Flags: ACK)
    ✅ client pod client-9f579495f-b2pcq was able to communicate with echo pod echo-same-node-7f877bbf9-p2xg8 (10.0.0.166)
    -------------------------------------------------------------------------------------------
    🔌 Validating from pod cilium-test/client-9f579495f-b2pcq to outside of cluster...
    -------------------------------------------------------------------------------------------
    ❌ Found RST in pod cilium-test/client-9f579495f-b2pcq
    ❌ FIN not found in pod cilium-test/client-9f579495f-b2pcq
    📄 Flow logs of pod cilium-test/client-9f579495f-b2pcq:
    Jan  6 13:41:22.025: 10.0.0.11:55334 -> 10.0.0.243:53 to-endpoint FORWARDED (UDP)
    Jan  6 13:41:22.025: 10.0.0.11:55334 -> 10.0.0.243:53 to-endpoint FORWARDED (UDP)
    Jan  6 13:41:22.027: 10.0.0.243:53 -> 10.0.0.11:55334 to-endpoint FORWARDED (UDP)
    Jan  6 13:41:22.028: 10.0.0.243:53 -> 10.0.0.11:55334 to-endpoint FORWARDED (UDP)
    Jan  6 13:41:22.028: 10.0.0.11:56466 -> 10.0.0.104:53 to-endpoint FORWARDED (UDP)
    Jan  6 13:41:22.028: 10.0.0.11:56466 -> 10.0.0.104:53 to-endpoint FORWARDED (UDP)
    Jan  6 13:41:22.029: 10.0.0.104:53 -> 10.0.0.11:56466 to-endpoint FORWARDED (UDP)
    Jan  6 13:41:22.029: 10.0.0.104:53 -> 10.0.0.11:56466 to-endpoint FORWARDED (UDP)
    Jan  6 13:41:22.030: 10.0.0.11:57691 -> 10.0.0.243:53 to-endpoint FORWARDED (UDP)
    Jan  6 13:41:22.030: 10.0.0.243:53 -> 10.0.0.11:57691 to-endpoint FORWARDED (UDP)
    Jan  6 13:41:22.030: 10.0.0.11:57691 -> 10.0.0.243:53 to-endpoint FORWARDED (UDP)
    Jan  6 13:41:22.031: 10.0.0.243:53 -> 10.0.0.11:57691 to-endpoint FORWARDED (UDP)
    Jan  6 13:41:22.031: 10.0.0.11:52849 -> 10.0.0.104:53 to-endpoint FORWARDED (UDP)
    Jan  6 13:41:22.032: 10.0.0.104:53 -> 10.0.0.11:52849 to-endpoint FORWARDED (UDP)
    Jan  6 13:41:22.033: 10.0.0.11:52849 -> 10.0.0.104:53 to-endpoint FORWARDED (UDP)
    Jan  6 13:41:22.037: 10.0.0.104:53 -> 10.0.0.11:52849 to-endpoint FORWARDED (UDP)
    Jan  6 13:41:22.038: 10.0.0.11:45040 -> 172.217.168.46:443 to-stack FORWARDED (TCP Flags: SYN)
    Jan  6 13:41:22.041: 172.217.168.46:443 -> 10.0.0.11:45040 to-endpoint FORWARDED (TCP Flags: SYN, ACK)
    Jan  6 13:41:22.041: 10.0.0.11:45040 -> 172.217.168.46:443 to-stack FORWARDED (TCP Flags: ACK)
    Jan  6 13:41:22.059: 10.0.0.11:45040 -> 172.217.168.46:443 to-stack FORWARDED (TCP Flags: ACK, PSH)
    Jan  6 13:41:22.073: 172.217.168.46:443 -> 10.0.0.11:45040 to-endpoint FORWARDED (TCP Flags: ACK, PSH)
    Jan  6 13:41:22.096: 10.0.0.11:45040 -> 172.217.168.46:443 to-stack FORWARDED (TCP Flags: ACK, RST)
    Jan  6 13:41:22.097: 172.217.168.46:443 -> 10.0.0.11:45040 to-endpoint FORWARDED (TCP Flags: ACK, FIN)
    Jan  6 13:41:22.097: 10.0.0.11:45040 -> 172.217.168.46:443 to-stack FORWARDED (TCP Flags: RST)
    ✅ client pod client-9f579495f-b2pcq was able to communicate with cilium.io
    -------------------------------------------------------------------------------------------
    🔌 Validating from pod cilium-test/client-9f579495f-b2pcq to local host...
    -------------------------------------------------------------------------------------------
    📄 Flow logs of pod cilium-test/client-9f579495f-b2pcq:
    Jan  6 13:41:25.305: 10.0.0.11 -> 192.168.64.25 to-stack FORWARDED (ICMPv4 EchoRequest)
    Jan  6 13:41:25.305: 192.168.64.25 -> 10.0.0.11 to-endpoint FORWARDED (ICMPv4 EchoReply)
    ✅ client pod client-9f579495f-b2pcq was able to communicate with local host
    -------------------------------------------------------------------------------------------
    🔌 Validating from pod cilium-test/client-9f579495f-b2pcq to service echo-same-node...
    -------------------------------------------------------------------------------------------
    📄 Flow logs of pod cilium-test/client-9f579495f-b2pcq:
    Jan  6 13:41:30.499: 10.0.0.11:39559 -> 10.0.0.104:53 to-endpoint FORWARDED (UDP)
    Jan  6 13:41:30.499: 10.0.0.11:39559 -> 10.0.0.104:53 to-endpoint FORWARDED (UDP)
    Jan  6 13:41:30.500: 10.0.0.104:53 -> 10.0.0.11:39559 to-endpoint FORWARDED (UDP)
    Jan  6 13:41:30.500: 10.0.0.104:53 -> 10.0.0.11:39559 to-endpoint FORWARDED (UDP)
    Jan  6 13:41:30.503: 10.0.0.11:59414 -> 10.0.0.166:8080 to-endpoint FORWARDED (TCP Flags: SYN)
    Jan  6 13:41:30.503: 10.0.0.166:8080 -> 10.0.0.11:59414 to-endpoint FORWARDED (TCP Flags: SYN, ACK)
    Jan  6 13:41:30.503: 10.0.0.11:59414 -> 10.0.0.166:8080 to-endpoint FORWARDED (TCP Flags: ACK)
    Jan  6 13:41:30.503: 10.0.0.11:59414 -> 10.0.0.166:8080 to-endpoint FORWARDED (TCP Flags: ACK, PSH)
    Jan  6 13:41:30.505: 10.0.0.166:8080 -> 10.0.0.11:59414 to-endpoint FORWARDED (TCP Flags: ACK, PSH)
    Jan  6 13:41:30.509: 10.0.0.11:59414 -> 10.0.0.166:8080 to-endpoint FORWARDED (TCP Flags: ACK, FIN)
    Jan  6 13:41:30.509: 10.0.0.166:8080 -> 10.0.0.11:59414 to-endpoint FORWARDED (TCP Flags: ACK, FIN)
    Jan  6 13:41:30.509: 10.0.0.11:59414 -> 10.0.0.166:8080 to-endpoint FORWARDED (TCP Flags: ACK)
    ✅ client pod client-9f579495f-b2pcq was able to communicate with service echo-same-node

### ClusterMesh

Install Cilium & enable ClusterMesh in Cluster 1

    cilium install --cluster-id 1
    🔮 Auto-detected Kubernetes kind: GKE
    ℹ️  Cilium version not set, using default version "v1.9.1"
    🔮 Auto-detected cluster name: gke-cilium-dev-us-west2-a-tgraf-cluster1
    🔮 Auto-detected datapath mode: gke
    ✅ Detected GKE native routing CIDR: 10.52.0.0/14
    🚀 Creating resource quotas...
    🔑 Found existing CA in secret cilium-ca
    🔑 Generating certificates for Hubble...
    🚀 Creating service accounts...
    🚀 Creating cluster roles...
    🚀 Creating ConfigMap...
    🚀 Creating GKE Node Init DaemonSet...
    🚀 Creating agent DaemonSet...
    🚀 Creating operator Deployment...

    cilium clustermesh enable
    ✨ Validating cluster configuration...
    ✅ Valid cluster identification found: name="gke-cilium-dev-us-west2-a-tgraf-cluster1" id="1"
    🔑 Found existing CA in secret cilium-ca
    🔑 Generating certificates for ClusterMesh...
    ✨ Deploying clustermesh-apiserver...
    🔮 Auto-exposing service within GCP VPC (cloud.google.com/load-balancer-type=internal)


Install Cilium in Cluster 2

    cilium install --context gke_cilium-dev_us-west2-a_tgraf-cluster2 --cluster-id 2
    🔮 Auto-detected Kubernetes kind: GKE
    ℹ️  Cilium version not set, using default version "v1.9.1"
    🔮 Auto-detected cluster name: gke-cilium-dev-us-west2-a-tgraf-cluster2
    🔮 Auto-detected datapath mode: gke
    ✅ Detected GKE native routing CIDR: 10.4.0.0/14
    🚀 Creating resource quotas...
    🔑 Found existing CA in secret cilium-ca
    🔑 Generating certificates for Hubble...
    🚀 Creating service accounts...
    🚀 Creating cluster roles...
    🚀 Creating ConfigMap...
    🚀 Creating GKE Node Init DaemonSet...
    🚀 Creating agent DaemonSet...
    🚀 Creating operator Deployment...

    cilium clustermesh enable --context gke_cilium-dev_us-west2-a_tgraf-cluster2
    ✨ Validating cluster configuration...
    ✅ Valid cluster identification found: name="gke-cilium-dev-us-west2-a-tgraf-cluster2" id="2"
    🔑 Found existing CA in secret cilium-ca
    🔑 Generating certificates for ClusterMesh...
    ✨ Deploying clustermesh-apiserver...
    🔮 Auto-exposing service within GCP VPC (cloud.google.com/load-balancer-type=internal)

Connect Clusters

    cilium clustermesh connect --destination-context gke_cilium-dev_us-west2-a_tgraf-cluster2
    ✨ Extracting access information of cluster gke-cilium-dev-us-west2-a-tgraf-cluster2...
    🔑 Extracting secrets from cluster gke-cilium-dev-us-west2-a-tgraf-cluster2...
    ℹ️  Found ClusterMesh service IPs: [10.168.15.209]
    ✨ Extracting access information of cluster gke-cilium-dev-us-west2-a-tgraf-cluster1...
    🔑 Extracting secrets from cluster gke-cilium-dev-us-west2-a-tgraf-cluster1...
    ℹ️  Found ClusterMesh service IPs: [10.168.15.208]
    ✨ Connecting cluster gke_cilium-dev_us-west2-a_tgraf-cluster1 -> gke_cilium-dev_us-west2-a_tgraf-cluster2...
    🔑 Patching existing secret cilium-clustermesh...
    ✨ Patching DaemonSet with IP aliases cilium-clustermesh...
    ✨ Connecting cluster gke_cilium-dev_us-west2-a_tgraf-cluster2 -> gke_cilium-dev_us-west2-a_tgraf-cluster1...
    🔑 Patching existing secret cilium-clustermesh...
    ✨ Patching DaemonSet with IP aliases cilium-clustermesh...

### Encryption

Install a Cilium in a cluster and enable encryption with IPsec

    cilium install --encryption=ipsec
    🔮 Auto-detected Kubernetes kind: kind
    ✨ Running "kind" validation checks
    ✅ Detected kind version "0.9.0"
    ℹ️  Cilium version not set, using default version "v1.9.2"
    🔮 Auto-detected cluster name: kind-chart-testing
    🔮 Auto-detected IPAM mode: kubernetes
    🔑 Found existing CA in secret cilium-ca
    🔑 Generating certificates for Hubble...
    🚀 Creating Service accounts...
    🚀 Creating Cluster roles...
    🔑 Generated encryption secret cilium-ipsec-keys
    🚀 Creating ConfigMap...
    🚀 Creating Agent DaemonSet...
    🚀 Creating Operator Deployment...
    ⌛ Waiting for Cilium to be installed...
