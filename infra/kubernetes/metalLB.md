### 설치 전 확인 사항
다음은 모든 네임스페이스의 서비스 상태이다.  
중간의 LoadBalancer 를 보면  
External-IP 가 pending 중이다.  
이는 로드밸런서가 없어서 발생 하는 문제이며  
현 상태에서는 cluster 의 80번 포트로 접근 하고자 할 경우  
외부에서는 32075 포트로 접근을 시도해야 접근이 가능하다.  
이를 해결하기 위해 metalLB 를 설치하여 로드밸런싱을 할 것이다.  
만약 AWS, Google Cloud 등 혹은 로드밸런서가 있을경우는 해당 설치과정은 제외 한다.  
![](./img/Screenshot%202023-04-17%20at%2012.29.31%20AM.png)


metalLB 설치
```shell
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.13.9/config/manifests/metallb-native.yaml
```


로드밸런싱 소스 풀 IPAddressPool 입력
```shell
kubectl apply -f ipAddressPool.yaml
```
ipAddressPool.yaml 내용
```yaml
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: lb-pool
  namespace: metallb-system
spec:
  addresses:
  - 192.168.4.1-192.168.4.1
```
address 는 범위 입력, 단일, CIDR 방식등으로 입력 가능  
[config 참고](https://github.com/metallb/metallb/blob/main/design/pool-configuration.md)

metalLB 설치 완료 후의 service 목록이다.  
LoadBalancer 의 External-IP 를 보면 IPAddressPool 로 입력 한 주소의 하나가 부여되어 있다.  
이제 외부에서 80, 433 포트로 접근이 가능하다.  
![](./img/Screenshot%202023-04-17%20at%2012.43.53%20AM.png)


