# 목표
- 최초 하드웨어 서버 1대를 시작으로 증설 한다.
- 최소한의 휴먼 리소스로 client 부터 back-end 까지 개발하고 배포 한다.
- 추후 발생 할 하드웨어 증설을 고려 한다.
- 자체 repository 를 구성 한다.
- OAuth 서버를 자체 구성 한다.


# 개발 배포 흐름도
### back-end (java)
1. 소스 개발
2. push  
git 으로 소스를 배포 하고 main 브런치에 commit 이 올라올 경우 webhook 으로 jenkins 호출
3. jenkins pipeline
> - build
> - kubernetes rollout update  
> - openapi generator 로 자동 SDK 생성 및 배포 
 



# 필요 기술
### [kubernetes](kubernetes/kubernetes.md)
### [metalLB](metalLB.md)
### [jenkins](jenkins.md)
### [nexus](nexus.md)
### [JIB](JIB.md)
### [keycloak](keycloak.md)
### [swagger](swagger.md)
