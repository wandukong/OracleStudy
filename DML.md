# DML (Data Manipulation Language)

## ๐ถSELECT : ๋ ์ฝ๋ ๊ฒ์
```sql
select distinct | * | ์ปฌ๋ผ๋ช as ๋ณ์นญ, ์ปฌ๋ผ๋ช ๋ณ์นญ,....
from ํ์ด๋ธ์ด๋ฆ
[where ์กฐ๊ฑด์ ]
[order by ์ปฌ๋ผ๋ช desc | asc , .. ];
 ```
 
##### ์ปฌ๋ผ๋ช ์ง์ 
```sql
select empno "์ฌ์๋ฒํธ", name as ์ฑ๋ช, position ์ง๊ธ, pay ๊ธ์ฌ from emp2;
   ```
   - as ์๋ต ๊ฐ๋ฅ, **ํฐ ๋ฐ์ดํ**๋ก ๋ฌถ๊ธฐ
   
##### || : ์ปฌ๋ผ ๋ฐ์ดํฐ ๋ถ์ด๊ธฐ
```sql
select name || position "์ด๋ฆ+์ง๊ธ", pay ๊ธ์ฌ from emp2;
```
```
๋์ฌ์ฅ๋ํ์ด์ฌ	100000000
์ ๋ถ์ฅ๋ถ์ฅ		72000000
์ต์ผ๋๊ณผ์ฅ		50000000
```

##### distinct : ๊ณ ์  ๋ฐ์ดํฐ ์ถ๋ ฅ
```sql
select distinct position from emp2;	
```

## ๐ฆINSERT : ๋ ์ฝ๋ ์ถ๊ฐ
```sql
insert into ํ์ด๋ธ๋ช values(๊ฐ,๊ฐ,...); -- ๋ชจ๋  ์ปฌ๋ผ์ ๋ฐ์ดํฐ๋ฅผ ๋ฃ์๊ฒฝ์ฐ
insert into ํ์ด๋ธ๋ช(์ปฌ๋ ด๋ช,์ปฌ๋ผ๋ช,..) values(๊ฐ,๊ฐ,...);
```
```sql
insert into test values(1,'seungwan');
insert into test(name) values('park');
insert into test(number) values(1);
```

#### ๋ ์ฝ๋ ๋ณต์ฌ
- ํ๋ ๊ฐ์, ๋ฐ์ดํฐ ํ์์ด ์ผ์นํด์ผ ํ๋ค.
```sql
insert into ํ์ด๋ธ์ด๋ฆ ๋ณต์ฌํ  ํ์ด๋ธ ๋ด์ฉ;
```
```sql
insert into c_emp2_blank select * from emp2;
insert into c_emp2_blank select * from emp2 where deptno=1000;
insert into c_emp2_blank(empno, name, deptno,pay) select empno, name, deptno, pay from emp2;
```

## ๐ผUPDATE : ๋ ์ฝ๋ ์์ 
```sql
update ํ์ด๋ธ์ด๋ฆ set ์ปฌ๋ผ๋ช=๋ณ๊ฒฝ๊ฐ, ์ปฌ๋ผ๋ช=๋ณ๊ฒฝ๊ฐ, ์ปฌ๋ผ๋ช=๋ณ๊ฒฝ๊ฐ, ..... [ where ์กฐ๊ฑด์ ];
```
```sql
update de_test set gender='M'; -- ์ ์ฒด ๋ ์ฝ๋ ์์ 
update de_test set qq=100 where name='ss'; -- ์กฐ๊ฑด์ ๋ง๋ ๋ ์ฝ๋ ์์ 
update de_test set gender='M', hobby='์ ์๊ธฐ', name='๋์ฌ์' where name='๋์ฌ์ฅ'; -- ์ฌ๋ฌ ํ๋ ์์ 
```

## ๐ทDELETE : ๋ ์ฝ๋ ์ญ์ 
```sql
delete from ํ์ด๋ธ์ด๋ฆ; -- ์ ์ฒด ๋ ์ฝ๋ ์ญ์ 
delete from ํ์ด๋ธ์ด๋ฆ where ์กฐ๊ฑด; -- ์กฐ๊ฑด์ ๋ง๋ ๋ ์ฝ๋ ์ญ์ 
```
```sql
delete de_test where name='ss';
delete de_test where addr is Null;
```