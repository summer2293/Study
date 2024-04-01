## Global Interpreter Lock

여러개의 쓰레드가 파이썬 바이트코드를 한번에 하나만 사용할 수 있도록 락을 거는 것을 말한다. 

파이썬 가비지 컬렉터는, 메모리를 해제할때 레퍼런스 카운팅 변수를 체크하는데, 카운팅이 0이되면 가비지컬렉터에 의해 해제된다. 멀티 스레드 환경에서 두 스레드가 레퍼런스 카운터 변수를 동시에 값을 늘리거나 줄이는 `Race Condition`이 발생할 수 있다. 이러한 상황이 발생하면 메모리 누수가 발생하거나 객체에 대한 참조가 남아있는 데도 메모리를 잘못 해제할 수 있기 때문에 GIL 로 락을 건다.
CPU bound한 프로그램에 대해서 GIL은 락을 걸어, 성능향상보다 오히려 병목을 만들 수 있다.
I/O bound한 프로그램에서는 락이 걸리지 않는다.


## N+1 Problem

#### ORM

데이터베이스 테이블을 객체로 맵핑하여 SQL 명령어 없이도 프로그래밍 언어로 데이터를 조작할 수 있는 방법.

#### **Lazy Loading**

해당 쿼리셋을 호출하더라도, 이후 쿼리셋에 데이터를 직접 이용할 때 SQL 문을 호출하는 것이다. `user.posts` 그러다보니, related 테이블을 불러올 때 값을 가져오지않아 해당 값을 조회할 때 N 번 불리는 문제가 발생한다

#### **N+1 Problem**

ORM을 사용할 때 생기는 성능 이슈 중 하나. N건의 데이터를 가져오는 쿼리가 한번 발생하는게 아닌 N개의 쿼리가 실행되는 문제. related 된 테이블이 있을경우 실행된다. 사용자에 주문 목록을 불러올때 사용자 한번과 주문목록 N번을 불러온다고 해서 N+1 문제라고 불린다.

#### **해결**

eager-loading 방식으로 쿼리셋에서 한번에 불러오게 설정해서 N+1 문제를 해결할 수 있다.

django 에서는 `prefetch_related` 와 `select_releated` 를 사용하여 해결한다.

`select_related`는 같은 쿼리내에서 관련된 instance를 가져오는 것이다. inner join 을 통해 값을 가져온다.

##### select_related()

```python
# orm 
visitors = Visitor.objects.filter(visit_date__year=2022).select_related("person")
for visitor in visitors:
	print(f"{visitor.person.name}. visited on {visitor.visit_date}")
```

```sql
# sql 
SELECT
	visitors.id, visitors.person_id, visitors.visit_date,
    ...
    person.id, person.name,
    ...
FROM
	visitors
    INNER JOIN persons ON (visitors.person_id = person.id)
WHERE year(visit_date) = 2022
```

##### prefetch_related()

`prefetch_related` 는 첫번째 쿼리를 가져오고, 그 값으로 다음 쿼리를 보내 가져오는 방식이다

```python
# orm 
visitors = Visitor.objects.filter(visit_date__year=2022).prefetch_related("person")
for visitor in visitors:
	print(f"{visitor.person.name}. visited on {visitor.visit_date}")
```

```sql
# sql
SELECT id, person_id, visit_date, ...
FROM visitors
WHERE year(visit_date) = 2022

SELECT id, name, phone_number, ...
FROM person
WHERE id IN (%s, %s, ...)
```

OTM 이나 MTM 은 많은 리스트들을 가져와 캐싱을 하고, 그 값을 innerjoin 하여 IN 에 넣어 쿼리를 돌려 값을 가져온다.

OTO 는 `selected_related`  를 사용하고 OTM , MTM은 `prefetch_related`를 사용해 두번째 쿼리 결과를 제한할 수 있다.