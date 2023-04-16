## init
클러스터 생성
```shell
sudo kubeadm init \
  --pod-network-cidr=192.168.0.0/16 \
  --upload-certs \
  --control-plane-endpoint=k8s-master
```
![](./img/Screenshot%202023-04-16%20at%206.16.06%20PM.png)

설정값 복사
```shell
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

만약에 실행중인 계정이 root 일 경우 아래의 command 도 실행
```shell
export KUBECONFIG=/etc/kubernetes/admin.conf
```

위의 kubeadm init 의 결과 캡쳐를 보면  
join 하는 명령이 있다  
kubeadm join ~~~ 을 실행 하여 다른 하드웨어(node) 에서 join 한다.


## reset
쿠버네티스의 클러스터를 초기화 한다.  
reset 을 할 경우 기존에 설정, 구동한 데이터가 삭제되므로 주의 할 것.

```shell
sudo kubeadm reset
```

![](./img/Screenshot%202023-04-16%20at%205.42.22%20PM.png)

reset 완료 후 기존 값 삭제처리
```shell
sudo rm -rf /etc/cni/net.d
rm -rf ~/.kube
```
