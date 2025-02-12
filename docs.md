# I Update System, Install docker, containerd, k8s

### 1. Update System
```sudo apt update &&  sudo apt upgrade -y```

### 2. Disable swap
```
sudo swapoff -a # turn off swap
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab # make sure stupid swap never starts
```

### 3. Install dependencies
sudo apt install -y apt-transport-https ca-certificates curl gpg

### 4. Install runtime environment
```
sudo apt install containerd
```

### Documentation
```
https://github.com/containerd/containerd/blob/main/docs/getting-started.md
```

# II Installing kubernetes

### 1. Download public signing key
```
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```

### 2. Add kubernetes apt repo
```
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

### 3. Update apt package
```
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

### Troubleshoot
```
vim /var/lib/kubelet/config.yaml # cgroupDriver should be set to systemd
vim /etc/containerd/config.toml # cgroup should be set to false
```

### Documentation

https://v1-28.docs.kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/

https://v1-28.docs.kubernetes.io/docs/setup/production-environment/container-runtimes/#cgroup-drivers


# III Installing Calico

### 1. Initialize kubernetes repo
```
sudo kubeadm init --pod-network-cidr=192.168.0.0/16
```

### 2. Configure kubectl
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

### 3. Install Calico
```
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.29.2/manifests/tigera-operator.yaml

kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.29.2/manifests/custom-resources.yaml

watch kubectl get pods -n calico-system # Confirm the pods are running. Wait until each pod has status of running
```

### Troubleshoot
```
kubectl taint nodes --all node-role.kubernetes.io/control-plane- # remove taints on control plane

kubectl get nodes -o wide # confirm all nodes are running
```

### Documentation
```
https://docs.tigera.io/calico/latest/getting-started/kubernetes/quickstart
```

# IV Install calicoctl

### 1. Install as kubectl plugin
```
curl -L https://github.com/projectcalico/calico/releases/download/v3.29.2/calicoctl-linux-amd64 -o kubectl-calico

chmod +x kubectl-calico # set file as executable
```

### Troubleshoot
```
kubectl calico -h # Verifiy
```

### Documentation
```
https://docs.tigera.io/calico/latest/operations/calicoctl/install
```

# V Creating a kubeadm cluster

### Documentation
```
https://v1-28.docs.kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/
```

### Troubleshoot
```
https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm-reset/
```