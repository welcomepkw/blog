## step1: 사전 준비
OS hostname 설정
```shell
sudo hostname k8s-master
```
hosts 파일에  
서버에 할당 받은 ip 로 host 입력 값 확인
```shell
sudo vi /etc/hosts
```
yum update
```shell
sudo yum -y update && sudo systemctl reboot
```

## step2: install kubernetes
kubernetes repo 등록
```shell
sudo tee /etc/yum.repos.d/kubernetes.repo<<EOF
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF
```

관련 패키지 설치
```shell
sudo yum clean all && sudo yum -y makecache
sudo yum -y install epel-release vim git curl wget kubelet kubeadm kubectl --disableexcludes=kubernetes
```

kubernetes 버전 확인
```shell
kubeadm  version
```
![](./img/Screenshot%202023-04-16%20at%205.59.21%20PM.png)

```shell
kubeadm  version
```
![](./img/Screenshot%202023-04-16%20at%206.00.19%20PM.png)

## step3: SELinux, SWAP 정지
SELinux 정지
```shell
sudo setenforce 0
sudo sed -i 's/^SELINUX=.*/SELINUX=permissive/g' /etc/selinux/config
```
SWAP 정지
```shell
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
sudo swapoff -a
```

systemctl 설정
```shell
sudo modprobe overlay
sudo modprobe br_netfilter

sudo tee /etc/sysctl.d/kubernetes.conf<<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF

sudo sysctl --system
```

kubelt 자동 실행
```shell
sudo systemctl enable kubelet
```

## step4: install container runtime
container runtime 은 총 3종류가 있다
1. Docker  
   docker 그것.  
   kubernetes version 1.24 부터 지원 중단.
2. CRI-O  
   oepnshift 등 진영에서 사용중으로 알고 있음
3. Containerd  
   Docker 에서도 해당 runtime 을 사용하여 구동 함.  
   최초 이 runtime 을 사용하여 구성 하고자 하였으나 설치 중 이상이 있어 docker 로 변경 함

docker 로 runtime 을 구성 하였으므로 docker 설치만 안내

패키지 설치
```shell
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install docker-ce docker-ce-cli containerd.io
```

필수 디렉토리 생성
```shell
sudo mkdir /etc/docker
sudo mkdir -p /etc/systemd/system/docker.service.d
```

docker daemon config 설정
```shell
sudo tee /etc/docker/daemon.json <<EOF
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2",
  "storage-opts": [
    "overlay2.override_kernel_check=true"
  ]
}
EOF
```

docker service 구동 및 자동 실행 설정
```shell
sudo systemctl daemon-reload 
sudo systemctl restart docker
sudo systemctl enable docker
```

## step5: firewalld 설정
필요한 포트만 수정하여 구성할 수 있으나   
편의를 위해 전체 shutdown 함.
```shell
sudo systemctl disable --now firewalld
```
