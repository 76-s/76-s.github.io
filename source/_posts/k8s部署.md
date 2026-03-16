---
title: K8s集群部署
date: 2025-12-12 21:46:52
categories: 容器
tags: [kubernetes,k8s]
---

### 环境说明：三台Ubuntu24.04.2，IP分别为128、129、130，无法科学上网

#### 1、主机名配置与解析

- 修改主机名
- 配置hosts文件
- ping验证

#### 2、关闭swap空间

- 临时关闭 `swapoff -a` 
- 永久关闭 `vim /etc/fstab` 注释包含 swpa 的行
- `free -h` 或 `swapon -show` 验证

#### 3、时间同步

- 查看当前时间/时区 `timedatectl status` 
- 更改时区 `timedatectl set-timezone Asia/Shanghai` 
- 更改时间 
	- 手动更改 `date -s "2025-12-03 14:30:00"` 
	- 自动同步 `systemctl restart systemd-timesyncd` 重启服务会立即和配置文件 /etc/systemd/timesyncd.conf 中指定的ntp服务器同步下

#### 4、防火墙等安全策略

- 临时关闭防火墙或配置放行规则
- Ubuntu默认不使用SELinux（使用AppArmor），但AppArmor不会影响K8s实验

#### 5、内核模块与参数

```shell
# 1. 将需要加载的内核模块加入配置文件
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

# 2. 立即加载模块
sudo modprobe overlay
sudo modprobe br_netfilter

# 3. 配置内核参数并持久化
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1  #开启桥接流量经过 iptables 的检查，确保 Service 的流量规则生效
net.bridge.bridge-nf-call-ip6tables = 0  #同上，针对 IPv6 流量
net.ipv4.ip_forward                 = 1  #开启 Linux 主机的 IPv4 路由转发，使 Pod 可以跨节点通信
net.ipv6.conf.all.forwarding        = 0
net.ipv4.vs.conn_reuse_mode         = 0  #调整IPVS连接复用行为，推荐在系统内核>5.9时启用，在大型集群中性能更好
EOF

# 4. 使配置生效（无需重启）
sudo sysctl --system
```

#### 6、SSH免密登录（仅master节点，非必要，仅推荐）
#### 7、安装容器运行时CRI（containerd）

- 在线安装
	1. 添加Docker官方GPG密钥（用于验证软件包）
	2. 添加阿里云提供的Docker CE软件源
	3. 安装containerd
	4. 修改配置文件
```
# 添加Docker官方GPG密钥
sudo mkdir -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# 添加阿里云的Docker CE仓库镜像
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# 更新源并安装containerd
sudo apt-get update
sudo apt-get install -y containerd.io

# 备份原配置
cp /etc/containerd/config.toml /etc/containerd/config.toml.bak

# 生成包含CRI插件的完整配置文件
containerd config default > /etc/containerd/config.toml

# 修改配置文件
disabled_plugins = []  #确保括号内不含cri
SystemdCgroup = true  #使用systemd作为cgroup驱动
sandbox = "registry.aliyuncs.com/google_containers/pause:3.10.1"  #Kubernetes需要使用pause镜像作为sandbox容器

# 验证 
grep disabled_plugins /etc/containerd/config.toml
grep SystemdCgroup /etc/containerd/config.toml
grep sandbox /etc/containerd/config.toml

# 使用systemd管理containerd
sudo systemctl daemon-reload
sudo systemctl enable --now containerd
sudo systemctl restart containerd
sudo systemctl status containerd

# 镜像加速配置参考
https://xuanyuan.cloud/usage/containerd
```
- 离线安装
	1.下载二进制包离线安装
	2.下载rpm/deb包离线安装

#### 8、安装containerd 的管理包 crictl

```
## 选择版本 https://github.com/kubernetes-sigs/cri-tools/releases
 
wget https://github.com/kubernetes-sigs/cri-tools/releases/download/v1.34.0/crictl-v1.34.0-linux-amd64.tar.gz
 
tar xvf crictl-v1.23.0-linux-amd64.tar.gz -C /usr/local/bin
 
cat <<EOF> /etc/crictl.yaml
runtime-endpoint: unix:///run/containerd/containerd.sock
image-endpoint: unix:///run/containerd/containerd.sock
timeout: 10
debug: false
EOF
 
# 验证
crictl pull hello-world
crictl images
```

#### 9、安装kubeadm, kubelet和kubectl

```
# 安装依赖
sudo apt install -y apt-transport-https ca-certificates curl gpg conntrack

# 添加Kubernetes官方仓库
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.31/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.31/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt update

# 安装kubeadm、kubelet和kubectl
sudo apt install -y kubelet kubeadm kubectl

# 锁定版本防止自动更新
sudo apt-mark hold kubelet kubeadm kubectl

# 启用kubelet服务
sudo systemctl enable --now kubelet

# 验证安装
kubeadm version
kubectl version --client
```

#### 10、使用kubeadm初始化集群

```
# 在主节点执行
kubeadm init \
  --apiserver-advertise-address=192.168.19.128 \
  --pod-network-cidr=10.244.0.0/16 \
  --service-cidr=10.96.0.0/12 \
  --image-repository=registry.aliyuncs.com/google_containers \
  --cri-socket=unix:///var/run/containerd/containerd.sock \

# 主节点和工作节点分别执行输入后的提示信息！！！
# 在主节点验证
kubectl get nodes
kubectl get pods -A
```

#### 11、安装 CNI 网络插件

```
#下载并修改Calico配置文件使用国内镜像
curl -O https://raw.githubusercontent.com/projectcalico/calico/v3.28.0/manifests/calico.yaml
##替换镜像为国内镜像源
sed -i 's|docker.io/calico/|docker.m.daocloud.io/calico/|g' calico.yaml
sed -i 's|quay.io/calico/|quay.m.daocloud.io/calico/|g' calico.yaml
#应用修改后的配置文件
kubectl apply -f calico.yaml
#检查状态
kubectl get pods -n kube-system -w  

#后续升级Calico
##重新下载新版本的calico.yaml
curl -O https://raw.githubusercontent.com/projectcalico/calico/<新版>/manifests/calico.yaml
#修改镜像源后应用
kubectl apply -f calico.yaml
```

#### 12、部署一个pod看看

```
cat > nginx.yaml << EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: swr.cn-north-4.myhuaweicloud.com/ddn-k8s/docker.io/library/nginx:1.23.2
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
  labels:
    app: nginx
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
      protocol: TCP
      nodePort: 30080
      name: nginx
EOF

kubectl apply -f nginx.yaml
kubectl get pod
kubectl get svc
```


>9和10为安装方式（kubeadm），官方提供的“半自动化部署工具”。（目标是让部署一个符合“最佳实践”的、标准的 Kubernetes 集群变得快速和简单）

#### 相关说明：
kubeadm 本身并不直接提供 Kubernetes 的核心组件（如 kubelet、kube-apiserver 等）。它做了以下几件事：
1、检查环境：确保服务器满足所有前置条件（如讨论的1-8步）
2、拉取镜像：从互联网（通常是谷歌的镜像仓库 k8s.gcr.io，现在部分转向 registry.k8s.io）下载所有必需的 Kubernetes 核心组件镜像。
3、生成证书：为集群各个组件之间的安全通信生成所有必要的证书和密钥。
4、生成静态 Pod 清单：在 Master 节点上，它会将 kube-apiserver, kube-controller-manager, kube-scheduler 等组件的部署文件（称为 Static Pod Manifest）写入 /etc/kubernetes/manifests 目录，然后由该节点上的 kubelet 直接启动和管理这些 Pod。
5、配置 kubelet：与节点上的 kubelet 进程通信，对其进行配置。
6、生成加入令牌：生成一个安全的令牌，让 Worker 节点可以加入到集群中。


>kubeadm 在底层仍然是使用二进制的容器镜像来运行各个组件，但它极大地简化了手动配置的复杂性。


其他安装方式：二进制、全自动化部署工具、托管服务