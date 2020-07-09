---
title: 01 - Setting up Kubernetes Cluster
---

Using Ubuntu Distribution (Ubuntu Xenial LTS 16.04) as Base Image for the Virtual Machine. We will be building a Kubernetes Cluster

#### Setup Docker and Kubernetes Repositories

```shell
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

sudo add-apt-repository    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"

curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

cat << EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
```

#### Install Docker, Kubelet, KubeAdm and KubeCtl

```shell
sudo apt-get update

sudo apt-get install -y docker-ce=18.06.1~ce~3-0~ubuntu kubelet=1.17.0-00 kubeadm=1.17.0-00 kubectl=1.17.0-00

sudo apt-mark hold docker-ce kubelet kubeadm kubectl
```

#### Enable IPTable Bridge Call

```
echo "net.bridge.bridge-nf-call-iptables=1" | sudo tee -a /etc/sysctl.conf

sudo sysctl -p
```

-----

#### Initialize Cluster on Kube Master

```
# Initialize Kube Cluster
sudo kubeadm init --pod-network-cidr=10.244.0.0/16

# Setup Local KubeConfig
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

#### Install Flannel Networking
```
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/2140ac876ef134e0ed5af15c65e414cf26827915/Documentation/kube-flannel.yml
```

#### Join Master Node

```
sudo kubeadm join 172.31.47.84:6443 --token c06upw.nhz4qqqifxmqdzaf     --discovery-token-ca-cert-hash sha256:970ac46eb8847181b2ff5cf911836ef73c3ac103508739f3679e4933a1795775
```

{{% notice tip %}}
You can use `kubeadm token create --print-join-command` to get the join command.
{{% /notice %}}
