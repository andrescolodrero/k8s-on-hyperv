# k8s-on-hyperv
K8s multinode cluster on HyperV / Windows 10

# Initial Setup

Enable Hyper-V on windows 10 https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v

Get Ubuntu 20.04.1 LTS Images

HyperV Virtual Switch:
Create "Kubernetes-Switch" so we can use our Windows host to comunnicate to the k8s cluster, and the nodes can get packages from internet
TODO: create 2 different interfaces.

# Create Linux Ubuntu Nodes.
Idea: Create 3 machines. 1 master and 2 workers. Use etcd for storage and setup K8s Admin Dashboard.
