## master node taint 해제
최초 구성 시 서버 1기를 기준으로 생성 하므로  
master node 의 taint 를 해제 한다.  
taint 가 설정 되어 있을 경우 해당 node 에는 pod 을 배포 하지 않음.

taint 목록 확인
```shell
kubectl describe node k8s-master | grep Taints
```
![](./img/Screenshot%202023-04-16%20at%206.48.41%20PM.png)

taint 삭제
```shell
kubectl taint nodes k8s-master node-role.kubernetes.io/control-plane:NoSchedule-
```
