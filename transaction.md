# Transaction
> 데이터베이스에서 데이터를 처리하는 하나의 단위
- 여러 쿼리문들이 정상적으로 작성되었다면 정상 종료(데이터베이스 반영)된다.
- 하나라도 잘못되었다면 모든 명령이 취소되는 ALL-OR-NOTHING 방식으로 처리된다.

## 🛫commit
>  하나의 트랜잭션인 여러 DML 명령어를 정상적으로 데이터베이스에 반영하겠다는 의미의 명령어


## 🛬rollback
>  잘못된 명령이나 잘못된 데이터를 입력하는 등 문제가 발생하였을 때 하나의 트랜잭션을 취소하는 명령어
- 특정 영역을 나누어서 저장, 취소를 선택적으로 할 수 있다
```sql
savepoint 이름; -- SQL실행 전에 영역을 분할함.
...
rollback work to 이름; -- 특정 영역에 해당하는 부분까지 취소됨.
```
```sql
savepoint sp1;
update employees set salary = 111 where first_name like 'L%';
update employees set first_name = 'nico' where first_name = 'Lex';
rollback work to sp1; -- 두 개의 DML 명령어가 취소된다.
```