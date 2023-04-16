## network plugin 설치
전체 namespace 에서 구동중인 내용 모두 표시
```shell
kubectl get all --all-namespaces
```
pod 중 coredns 가 pending 중이다.  
network plugin 설치 시 정상 작동 예정.
![](./img/Screenshot%202023-04-16%20at%206.23.48%20PM.png)

network plugin 설치.  
종류는 여러가지가 있으나 Calico 설치 함.
```shell
VER=$(curl -s https://api.github.com/repos/projectcalico/calico/releases/latest|grep tag_name|cut -d '"' -f 4)

wget https://raw.githubusercontent.com/projectcalico/calico/${VER}/manifests/tigera-operator.yaml
kubectl create -f tigera-operator.yaml

wget https://raw.githubusercontent.com/projectcalico/calico/${VER}/manifests/custom-resources.yaml
kubectl create -f custom-resources.yaml
```

[Network addon list](https://kubernetes.io/docs/concepts/cluster-administration/addons/)

주의사항  
각 addon 마다 기본 네트워크 대역대가 기본 설정되어 있는듯 하다.    
admin init 시 --pod-network-cidr 값을 참조 하므로  
해당 값을 addon 에 맞게 입력 하거나 addon 설치 시 대역대를 변경하여 설치 한다.  
만약 대역대가 다르게 입력 될 경우  
network plugin 의 pod, service 등은 정상 구동 중이나 아이피 배분을 하지 못해  
coredns 가 정상 작동을 하지 않는다.  
만약 kubeadm init 단계에서 대역대를 변경 했다면
위 예제의 calico 설치 중 custom-resources.yaml 내용을 변경하여 설치 해야 한다.
