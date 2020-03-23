---
title: Setting up Kubernetes and Terraform
---

Create *kube-config.yml*

```yaml
apiVersion: kubeadm.k8s.io/v1beta2
kind: ClusterConfiguration
networking:
  podSubnet: 10.244.0.0/16
apiServer:
  extraArgs:
    service-node-port-range: 8000-31274
```

Initialize Kubernetes

```
sudo kubeadm init --config kube-config.yml
```

Copy `admin.conf` to your home directory

```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

Install Flannel:

```
sudo kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

Untaint Kubernetes Master
```
kubectl taint nodes --all node-role.kubernetes.io/master-
```