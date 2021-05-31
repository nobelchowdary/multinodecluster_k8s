# Configuring Master Node 
1. yum install docker -y
2. systemctl enable docker --now
>> vim /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-\$basearch
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
exclude=kubelet kubeadm kubectl
>> yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
>> systemctl enable --now kubelet
>> docker info | grep -i cgroup
>> systemctl restart docker
>> vim /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"]
}
>> docker info | grep -i cgroup
>> yum install iproute-tc
>> kubeadm init --pod-network-cidr=10.240.0.0/16 --ignore-preflight-errors=NumCPU --ignore-preflight-errors=Mem
>> mkdir -p $HOME/.kube
>> sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
>> sudo chown $(id -u):$(id -g) $HOME/.kube/config
>> kubectl get pods --all-namespaces
>> kubeadm token create  --print-join-command

# Configuring Slave Node
>> yum install docker -y
>> systemctl enable docker --now
>> vim /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-\$basearch
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
exclude=kubelet kubeadm kubectl
>> yum install -y  kubectl kubelet  kubeadm  --disableexcludes=kubernetes
>> systemctl enable kubelet --now
>> docker info | grep -i cgroup
>> vim /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"]
}
>> systemctl restart docker
>> docker info | grep -i 
>> yum install iproute-tc
>> vim /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
>> sysctl --system
>> (copy this line from the last command of master node) kubeadm join 172.31.40.209:6443 --token 3joamn.y8asi8hw2jb41xx5     --discovery-token-ca-cert-hash sha256:86dd3714b1168b8de0abcb63300a458baf838860ff9c6f53bd85707853ff4db7

# Configuring Network Settings at Master Node
>> kubectl apply  -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
>> kubectl get pods --all-namespaces
>> kubectl get nodes
