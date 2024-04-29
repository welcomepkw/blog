### Transaction 에 대한 기본내용
- DB 의 Transaction 은 @Transactional 어노테이션이 붙은 Method 에 적용된다.
- @Transactional 의 Method 에서 조회 한 데이터는 영속성 데이터로 처리한다.
- 특별한 설정등이 없는경우 commit 시점에 flush 가 일어난다.
- flush 시점에 dirty checking 이 일어나며 변경 데이터 쿼리를 실행 한다.

### Read-Only
- @Transactional(readOnly = true) 형태로 Read-Only Transaction 을 표시한다.
- 일반 Transaction 과 같이 DB 의 transaction 이 발생 한다.
- 단. 일반 @Transaction 과 다르게 자동으로 commit 시점에 flush 가 발생 하지 않고 종료 한다. (Read-Only 이기 때문. Not Write)
- 사용자가 flush 를 호출시 저장 처리 한다.
- clear 와 다르게 flush 시점에서 select 를 다시 호출 하지 않는것으로 보아 영속 상태가 managed 인것으로 보인다.

### Read-Only 에 대한 상세
- 메모리 관리를 위해 Read-Only 로 조회를 해야 하는것인가에 대한 고민을 했다.
- 결론은 서버(Not DB. Application 구동중인 서버)의 메모리 관리 측면으로만 보았을때는 @Transactional 을 사용하지 않는것이 최선이다.
- @Transactional(readOnly = true) 를 사용하는 경우는 영속성 데이터를 보유하고 Transaction 내에서 Lazy-Loading 을 사용하고자 할 때 이다.

### clear, detach
- detach 는 특정 entity의 영속성 관리를 분리(detached) 할때 사용한다.
- clear 는 영속성 컨텍스트 영역의 데이터를 clear 해서 모두 detached 상태로 변경 한다.
- detached 상태는 영속성 데이터의 연결이 끊긴 상태이므로 save 후 flush 를 하려고 할 경우 select query 를 다시 실행하여 데이터를 초기화 후 저장 프로세스를 실행 한다.

### close
- clear 는 영속성 컨텍스트 저장공간(1차 cache, 쓰기 지연 공간) 데이터를 초기화 하는 방법이라면
- close 는 해당 영역을 종료 한다.
- 라고 나와 있으나 close 에대한 내용은 조금더 깊게 알아볼 필요가 있어 보인다.
- clear 의 경우 save 시점에 select 를 다시하여 데이터를 초기화 하지만 close 의 경우 select 로 초기화 하는 쿼리 발생 시점이 없다. 이는 영속성 데이터가 보존 되고 있는것으로 예상 되며
- close 시 cache 데이터는 보존 하지만 단순히 연결만 끊는것이 아닌가 하는 예상을 해본다.

### 대용량 batch 프로세스에 대한 의견
- 단순 데이터 조회의 경우 @Transaction 을 사용하지 않는것이 메모리 관리 측면에서 유리하다.
- @Transaction 을 사용해야 하는 경우 batch 사이즈를 일정 단위로 적당히 자르고 각 loop 마다 clear 로 컨텍스트 공간을 초기화 한다.
