## index

- [Join](##join)
- [Transaction](##Transaction)
- [Transaction ACID](##Transaction ACID )
- [Transaction Isolated Level](Transaction Isolated Level)



## Join

데이터베이스의 여러 테이블의 레코드를 하나로 결합하기 위해 사용하는 방법. 조인을 해서 새로운 테이블로 저장하거나, 그 결과를 이용할 수 있는 레코드를 만들어낸다

**INNER JOIN**

가장 흔한 결합방식으로, 각각 컬럼에 있는 공통 값을 조합해서 새로운 결과 테이블을 생성한다.

명시적 조인 표현

```sql
SELECT * FROM employee INNER JOIN department ON employee.DepartmentID = department.DepartmentID;
```

암시적 조인 표현

join 단어를 직접 안쓰고도 join 이 가능한것을 암시적 조인 표현이라고 한다.

```sql
SELECT * FROM employee, department WHERE employee.DepartmentID = department.DepartmentID;
```





## Transaction

분리 불가능한 수행 작업의 단위를 의미한다. 작업의 단계가 여러개가 있을 때, 이 작업을 완벽하게 처리하지 못 할경우 원 상태로 복구해서 작업의 일부만 적용되지 않게 방지해야합니다. 여러 단계의 작업이 존재할 때 이 작업들이 모두 성공적으로 완료되어야 성공적으로 완료될 수 있게 해야합니다. 
    
## Transaction ACID 

##### 원자성

트랜잭션이 내 모든 작업이 완전이 수행되거나 수행되지 않게 합니다. 즉 하나라도 실패하면 다시 이전의 상태로 돌아가서 일부만 완료되는 상태를 제공하지 않아야합니다.    

##### 일관성

트랜잭션이 완료되더라도 일어나기 전에 상황과 동일하게 일관성을 보장해야합니다.

##### **고립성**

트랜잭션은 독립적으로 수행되어야합니다

##### **지속성**

트랜잭션이 정상적으로 종료된 이후에 데이터베이스에 결과가 저장이 되어야 합니다.
    

## Transaction Isolated Level

트랜잭션이 동시에 처리될때 특정 트랜잭션의 상태를 어느정도 조회할 수 있는지를 이야기한다.
다른 트랜잭션에서 변경하거나 조회하는 데이터를 볼지 말지를 의미한다.

**READ UNCOMMITTED**

각 트랜잭션에서 변경 내용을 다른 트랜잭션에서 볼 수 있다. 

*Dirty Read* : 트랜잭션 작업이 커밋이 안되었을때 다른 트랜잭션에서 조회할경우, 커밋이 안되고 롤백이 되었을 때 문제가 생긴다.

**READ COMMITTED**

커밋이 된 데이터를 조회할 수 있는 것

*NON-REPEATABLE READ* : 하나의 트랜잭션에서 시차를 적용해 select 조회를 2번 조회를 했을때, 그동안 다른 트랜잭션에서 업데이트가 되어서 조회한 값을 다르게 가져오는 경우가 발생할 수 있다. 정합성이 깨지는 문제가 있다.

##### **REPEATABLE READ**

자기 트랜잭션이 생성되기 이전의 트랜잭션에서 커밋이 된 데이터만 읽게하는 단계

mysql, mariadb 사용. 트랜잭션 ID 를 부여하여 트랜잭션 ID 보다 작은 트랜잭션 번호에서 변경한 것만 읽는다.

*PHANTOM READ* : 집합의 수가 변경될 수 있음. 첫 번째 조회와 두 번째 조회 사이에 다른 트랜잭션에 의해 새로운 행이 삽입되거나 삭제되어 조회 결과의 행의 수가 달라지는 현상을 말한다. select 에 관련해서는 쿼리를 걸 수 있으나 없는 행에 대해서는 lock 을 걸수 없으므로 즉, "유령(phantom)" 행이 나타나거나 사라져서, 범위 쿼리의 결과가 달라진다.

##### **Serializable**

가장 고수준의 격리수준으로 트랜잭션을 무조건 **순차적으로 진행**시킨다. 

SELECT 쿼리 실행 시 Shared Lock을, INSERT, UPDATE, DELETE 쿼리 실행 시 Exclusive Lock (MySQL의 경우 Nexy Key Lock)을 걸어버려서 관련 이슈가 생기지 않게 진행한다.

##### 참조

[[DB] 트랜잭션 격리수준 (Isolation Level) 에 쉽게 이해하기](https://tlatmsrud.tistory.com/118)

