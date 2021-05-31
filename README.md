# Configuring Master Node 
01 - yum install docker -y
02 - systemctl enable docker --now
03 - vim /etc/yum.repos.d/kubernetes.repo
04 - 
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-\$basearch
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
exclude=kubelet kubeadm kubectl
05 - yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
06 - systemctl enable --now kubelet
07 - docker info | grep -i cgroup
08 - systemctl restart docker
09 - vim /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"]
}
10 - docker info | grep -i cgroup
11 - yum install iproute-tc
12 - kubeadm init --pod-network-cidr=10.240.0.0/16 --ignore-preflight-errors=NumCPU --ignore-preflight-errors=Mem
13 - mkdir -p $HOME/.kube
14 - sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
15 - sudo chown $(id -u):$(id -g) $HOME/.kube/config
16 - kubectl get pods --all-namespaces
17 - kubeadm token create  --print-join-command

# Configuring Slave Node
01 - yum install docker -y
02 - systemctl enable docker --now
03 - vim /etc/yum.repos.d/kubernetes.repo
04 - 
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-\$basearch
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
exclude=kubelet kubeadm kubectl
05 - yum install -y  kubectl kubelet  kubeadm  --disableexcludes=kubernetes
06 - systemctl enable kubelet --now
07 - docker info | grep -i cgroup
08 - vim /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"]
}
09 - systemctl restart docker
10 - docker info | grep -i 
11 - yum install iproute-tc
12 - vim /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
13 - sysctl --system
14 - (copy this line from the last command of master node) kubeadm join 172.31.40.209:6443 --token 3joamn.y8asi8hw2jb41xx5     --discovery-token-ca-cert-hash sha256:86dd3714b1168b8de0abcb63300a458baf838860ff9c6f53bd85707853ff4db7

# Configuring Network Settings at Master Node
1 - kubectl apply  -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
2 - kubectl get pods --all-namespaces
3 - kubectl get nodes
