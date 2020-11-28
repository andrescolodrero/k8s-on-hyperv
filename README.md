# k8s-on-hyperv
K8s multinode cluster on HyperV / Windows 10

# Initial Setup

Enable Hyper-V on windows 10 https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v

Get Ubuntu 20.04.1 LTS Images

HyperV Virtual Switch:
Create "Kubernetes-Switch" so we can use our Windows host to comunnicate to the k8s cluster, and the nodes can get packages from internet
TODO: create 2 different interfaces.

# Create BAse Image for Linux Nodes/worker
Idea: Create 3 machines. 1 master and 2 workers. Use etcd for storage and setup K8s Admin Dashboard.

Installing and Testing the Components of a Kubernetes Cluster


Get the Docker gpg, and add it to your repository.
In all three terminals, run the following command to get the Docker gpg key:

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

Then add it to your repository:

sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

Get the Kubernetes gpg key, and add it to your repository.
In all three terminals, run the following command to get the Kubernetes gpg key:

curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

Then add it to your repository:

<code> 
  cat << EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
  
deb https://apt.kubernetes.io/ kubernetes-xenial main

EOF

</code>

Update the packages:
<code>
sudo apt update
  </code>
Install Docker, kubelet, kubeadm, and kubectl.
In all three terminals, run the following command to install Docker, kubelet, kubeadm, and kubectl:
<code>
sudo apt install -y docker-ce=5:19.03.10~3-0~ubuntu-focal kubelet=1.18.5-00 kubeadm=1.18.5-00 kubectl=1.18.5-00
  </code>

Turnoff 
sudo swapoff -a
To persist changes, uncomment swap in etc/fstab
Add the iptables rule to sysctl.conf:
echo "net.bridge.bridge-nf-call-iptables=1" | sudo tee -a /etc/sysctl.conf
Enable iptables immediately:

sudo sysctl -p

# Create KUbernetes Master

Initialize the Kubernetes cluster.
In the Controller server terminal, run the following command to initialize the cluster using kubeadm/

sudo kubeadm init --pod-network-cidr=[your cidr], bye example:172.29.0.0/16
### Set up local kubeconfig.
In the Controller server terminal, run the following commands to set up local kubeconfig:

sudo mkdir -p $HOME/.kube

sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config

sudo chown $(id -u):$(id -g) $HOME/.kube/config

Apply the flannel CNI plugin as a network overlay.
An overlay network abstracts a physical (underlay) network to create a virtual network. It provides simpler interface by hiding complexities of the underlay.
In the Controller server terminal, run the following command to apply flannel:

kubectl apply -f https://docs.projectcalico.org/v3.14/manifests/calico.yaml
Join the worker nodes to the cluster, and verify they have joined successfully.
When we ran sudo kubeadm init on the Controller node, there was a kubeadmin join command in the output. You'll see it right under this text:

You can now join any number of machines by running the following on each node as root:
To join worker nodes to the cluster, we need to run that command, as root (we'll just preface it with sudo) on each of them. It should look something like this:

if forget, recreate the join command:
kubeadm token create --print-join-command

sudo kubeadm join <your unique string from the output of kubeadm init>
Run a deployment that includes at least one pod, and verify it was successful.
In the Controller server terminal, run the following command to run a deployment of ngnix:

# Test Deployment
To check if the new node enjoy the cluster:
Kubectl get nodes
andres@masterk8s:~$ kubectl get nodes
NAME        STATUS   ROLES    AGE   VERSION
masterk8s   Ready    master   13h   v1.18.5
node1-k8s   Ready    <none>   13h   v1.18.5

We could test now a deployment
kubectl create deployment nginx --image=nginx
Verify its success:

kubectl get deployments
Verify the pod is running and available.
In the Controller server terminal, run the following command to verify the pod is up and running:

kubectl get pods

Use port forwarding to extend port 80 to 8081, and verify access to the pod directly.
In the Controller server terminal, run the following command to forward the container port 80 to 8081 (replace <pod_name> with the name in the output from the previous command):

kubectl port-forward <pod_name> 8081:80
Open a new terminal session and log in to the Controller server. Then, run this command to verify we can access this container directly:

curl -I http://127.0.0.1:8081
We should see a status of OK.

Execute a command directly on a pod.
In the original Controller server terminal, hit Ctrl+C to exit out of the running program.

Still in Controller, execute the nginx version command from a pod (using the same <pod_name> as before):

kubectl exec -it <pod_name> -- nginx -v

Create a service, and verify connectivity on the node port.
In the original Controller server terminal, run the following command to create a NodePort service:

kubectl expose deployment nginx --port 80 --type NodePort
View the service:

kubectl get services
Get the node the pod resides on.

kubectl get po -o wide
Verify the connectivity by using curl on the NODE from the previous step and the port from when we viewed the service. Make sure to replace YOUR_NODE and YOUR_PORT with appropriate values for this lab.

curl -I YOUR_NODE:YOUR_PORT
We should see a status of OK.

Conclusion
Congratulations on completing this lab!
