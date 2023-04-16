## init
클러스터 생성  
pod-network-cidr 을 입력하여 cluster 에 부여 할 아이피 대역대를 선택한다.  
[CIDR 입력법](https://ko.wikipedia.org/wiki/CIDR)  
주의 할 점은 서버가 속한 네트워크의 대역대와 겹치지 않게 해야 한다.
```shell
sudo kubeadm init \
  --pod-network-cidr=192.168.0.0/16 \
  --upload-certs \
  --control-plane-endpoint=k8s-master
```
![](./img/Screenshot%202023-04-16%20at%206.16.06%20PM.png)

> **init 시 오류 발생하는 경우**  
> 오류 화면 캡쳐는 없으나 CRI 가 구동중이 않다 라는 오류 였던것 같다.    
> 이는 docker runtime 으로 설치 할 경우 발생 하는것으로 예상 되며  
> containerd 의 cri plugin 이 기본 disable 상태여서 그렇다.
> 아래 설정 파일을 열어서 disable 구문을 주석 처리 한다. 
> ```shell
> sudo vi /etc/containerd/config.toml
> ```
> ![](./img/Screenshot%202023-04-17%20at%201.00.06%20AM.png)
> containerd 재시작
> ```shell
> sudo systemctl restart containerd
> ```



config 설정
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
