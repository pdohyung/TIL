## 조인의 종류

조인이란 두 개 이상의 테이블을 묶어서 하나의 결과물을 만드는 것이다.  
MySQL에서는 join이라는 쿼리로, MongoDB에서는 lookup이라는 쿼리로 이를 처리한다.  
하지만 MongoDB의 lookup 조인 연산은 관계형 데이터베이스보다 성능이 떨어지기 때문에 되도록 사용하지 말아야 한다.

> **내부 조인 (inner join):** 왼쪽 테이블과 오른쪽 테이블의 두 행이 모두 일치하는 행이 있는 부분만 표기  
> **왼쪽 조인 (left outer join):** 왼쪽 테이블의 모든 행이 결과 테이블에 표기  
> **오른쪽 조인 (right outer join):** 오른쪽 테이블의 모든 행이 결과 테이블에 표기  
> **합집합 조인 (full outer join):** 두 개의 테이블을 기반으로 조건에 만족하지 않은 행까지 모두 표기

### 내부 조인

두 테이블 간의 교집합을 나타낸다.

```sql
SELECT *
FROM TableA A
INNER JOIN TableB B
ON A.key = B.key
```

### 왼쪽 조인

테이블 B에서 일치하는 부분의 레코드와 함께 테이블 A를 기준으로 완전한 레코드 집합을 생성  
만약 테이블 B에 일치하는 항목이 없다면 해당 값은 null 값이 된다.

```sql
SELECT *
FROM TableA A
LEFT JOIN TableB B
ON A.key = B.key
```

### 오른쪽 조인

테이블 A에서 일치하는 부분의 레코드와 함께 테이블 B를 기준으로 완전한 레코드 집합을 생성  
만약 테이블 A에 일치하는 항목이 없으면 해당 값은 null 값이 된다.

```sql
SELECT *
FROM TableA A RIGHT JOIN TableB B
ON A.key = B.key
```

### 합집합 조인

양쪽 테이블에서 일치하는 레코드와 함께 테이블 A와 테이블 B의 모든 레코드 집합을 생성  
이때 일치하는 항목이 없다면 누락된 값 쪽에 null 값이 포함되어 출력된다.

```sql
SELECT *
FROM TableA A FULL OUTER JOIN TABLEB B
ON A.key = B.key
```

## 조인의 원리

조인은 조인의 원리인 중첩 루프 조인, 정렬 병합 조인, 해시 조인을 기반으로 조인 작업이 이루어진다.

### 중첩 루프 조인

중첩 루프 조인은 중첩 for 문과 같은 원리로 조인하는 방법이며, 랜덤 접근에 대한 비용이 많이 증가하므로 대용량 테이블에는 적합하지 않다.   
예를 들어 t1, t2 테이블을 조인하면 t1 테이블의 행을 하나 읽고, t2 테이블의 행도 하나 읽어 조건에 맞는 레코드를 찾아 반환한다.  
참고로 중첩 루프 조인에서 발전한, 조인할 테이블을 작은 블록으로 나눠서 블록 하나씩 조인하는 블록 중첩 루프 조인이라는 방식도 있다.

### 정렬 병합 조인

정렬 병합 조인은 각각의 테이블을 조인할 필드 기준으로 정렬하고, 조인 작업을 수행한다.  
조인할 때 쓸 적절한 인덱스가 없고 대용량 테이블 조인, 조인 조건으로 <, > 등 범위 비교 연산자가 있을 때 쓴다.

### 해시 조인

해시 조인은 해시 테이블을 기반으로 조인하는 방법이고, 동등(=) 조인에서만 사용할 수 있다.  
두 개의 테이블을 조인한다고 했을 때 하나의 테이블이 메모리에 온전히 들어간다면 보통 중첩 루프 조인보다 효율적이다. (메모리에 올리지 못한다면 디스크를 사용하는 비용이 발생)  
MySQL의 해시 조인 단계는 빌드 단계와 프로브 단계로 나뉜다.

> **빌드 단계**  
> - 빌드 단계는 입력 테이블 중 하나를 기반으로 인-메모리 해시 테이블을 빌드 한다.
> - 예를 들어 persons와 countries라는 테이블을 조인한다고 했을 때, 둘 중 바이트가 더 작은 테이블을 기반으로 해시 테이블을 빌드한다.
> - 또한 조인에 사용되는 필드가 해시 테이블의 키로 사용된다. (해당 예제에서는 countries.country_id)

> **프로브 단계**  
> - 프로브 단계동안 레코드를 읽으며, 각 레코드에서 persons.country_id에 일치하는 레코드를 찾아서 결괏값을 반환한다.
> - 해시 조인은 각 테이블을 한 번씩만 읽어서 두 개의 테이블을 읽는 중첩 루프 조인보다 보통은 성능이 더 좋다.
> - 참고로 사용 가능한 메모리양은 시스템 변수 join_buffer_size에 의해 제어되며, 런타임 시 조정할 수 있다.
