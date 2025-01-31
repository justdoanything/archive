<!-- TOC -->
* [SQL](#sql)
  * [ORDER BY INTSTR (검색 조건과 가장 많이 일치하는 순으로 정렬하고 싶을 때)](#order-by-intstr-검색-조건과-가장-많이-일치하는-순으로-정렬하고-싶을-때)
  * [ORDER BY FIELD (특정한 값을 우선적으로 정렬하고 싶을 때)](#order-by-field-특정한-값을-우선적으로-정렬하고-싶을-때)
  * [ORDER BY CASE (결과값의 첫자리가 숫자 -> 한글 -> 영어 -> 그 외(특수문자)로 정렬하고 싶을 때)](#order-by-case-결과값의-첫자리가-숫자---한글---영어---그-외특수문자로-정렬하고-싶을-때)
  * [IGNORE CASE & INSERT ON DUPLICATATE UPDATE](#ignore-case--insert-on-duplicatate-update)
  * [REGEXP & REPLACE (구분자로 여러 값을 한번에 조회하고 싶을 때)](#regexp--replace-구분자로-여러-값을-한번에-조회하고-싶을-때)
  * [MySQL Table Lock 해결](#mysql-table-lock-해결)
* [8. 성능 개선을 위한 Query 최적화](#8-성능-개선을-위한-query-최적화)
<!-- TOC -->

# SQL
## ORDER BY INTSTR (검색 조건과 가장 많이 일치하는 순으로 정렬하고 싶을 때)
- 검색 조건과 가장 많이 일치하는 순으로 정렬하고 싶을 때 INTSTR(검색 조건으로 시작하는 인덱스를 반환)을 사용한다.
- `yong`으로 검색한 결과가 `yongwoo`, `leeyong`, `lyong` 라고 했을 때 _INSTR_ 을 사용하면 `GROUP BY 1, 4, 2, name` 순으로 되고 yongwoo, lyong, leeyong 순으로 정렬할 수 있다.
  ```sql
  SELECT *
  FROM member m
  WHERE m.name LIKE CONCAT('%', ${name}, '%')
  ORDER BY INSTR(name, #{name}), name
  ```

## ORDER BY FIELD (특정한 값을 우선적으로 정렬하고 싶을 때)
- 특정한 값을 우선적으로 정렬할 때 사용한다
  ```sql
  SELECT *
  FROM category c
  ORDER BY FIELD(c.category_name, '국내', '해외'), c.category_name, c.sort_order
  ```
- 예를들어 우선 순위로 조회한 값의 id가 [4, 7, 9, 5] 라고 할 때, [4, 7, 9, 5]는 정렬조건을 부여한 값이기 때문에 이 중에 상위 3개를 조회하고 싶다면 아래와 같이 해야한다.
  만약 그냥 조회한다면 [4, 5, 7, 9] 순으로 조회가 된다.
  ```sql
  SELECT *
  FROM community c
  WHERE ids IN
    <foreach collection="list" item="value" separator="," open="(" close=")">
        #{value}
    </foreach>
  ORDER BY FIELD(c.community_id,
    <foreach collection="list" item="value" separator=",">
        #{value}
    </foreach>
  LIMIT 3
  ```

## ORDER BY CASE (결과값의 첫자리가 숫자 -> 한글 -> 영어 -> 그 외(특수문자)로 정렬하고 싶을 때)
- name의 첫자리가 숫자 -> 한글 -> 영어 -> 그 외(특수문자)로 정렬하는 방법
  ```sql
  ORDER BY CASE
    WHEN name REGEXP '^[0-9]' THEN 1
    WHEN name REGEXP '^[ㄱ-ㅎ가-힣]' THEN 2
    WHEN name REGEXP '^[a-zA-Z]' THEN 3
    ELSE 4 END, name
  ```

## IGNORE CASE & INSERT ON DUPLICATATE UPDATE
- `INSERT IGNORE ...` : 중복 키 에러가 발생했을 때 신규로 입력되는 레코드는 무시하고 AUTO_INCREMENT 되지 않음.
- `REPLACE IGNORE ...` : 중복 키 에러가 발생했을 때 기존 레코드는 삭제하고 신규 레코드를 삽입하는 방식이다.
- `INSERT INTO ... ON DUPLICATE UPDATE ...` : 중복 키 에러가 발생했을 때 원하는 값을 직접 설정할 수 있다.
    ```sql
    INSERT INTO employee VALUES ('name', 'city')
    ON DUPLICATE KEY UPDATE city = VALUES(city)
    ```

## REGEXP & REPLACE (구분자로 여러 값을 한번에 조회하고 싶을 때)
- 구분자로 여러 값을 한번에 조회하고 싶을 때 사용
```sql
SELECT *
FROM users
WHERE user_name REGEXP REPLACE(REPLACE(#{userNames},' ',''),',','|')
```

## MySQL Table Lock 해결
- ALTER TABLE을 할 때 LOCK을 방지하기 위한 옵션
  ```sql
  ALTER TABLE ...
  , ALGORITHM=INPLACE, LOCK=NONE;
  ```
- 일반적으로 LOCK 걸린 테이블을 확인하고 해결하는 방법
  ```sql
  # LOCK 확인
  SHOW PROCESSLIST;

  # 프로세스 삭제
  KILL ${PID};
  ```
- 하지만 `SHOW PROCESSLIST`에서 발견되지 않는 lock이 존재할 수 있습니다. `SHOW OPEN TABLES` 명령을 통해서 In_use 값을 조회하거나 의심되는 테이블에 조건을 걸어서 조회할 수 있습니다.
  ```sql
  SHOW OPEN TABLES WHERE In_use > 0;
  ```
- `SHOW OPEN TABLES` 명령어를 통해서 의심되는 테이블을 찾았다고 해도 `SHOW PROCESSLIST`에서 PID가 조회되지 않기 때문에 어떤 조치를 취하기 어렵습니다.
- MySQL의 메타데이터 락
  - 공식문서 : https://dev.mysql.com/doc/refman/8.0/en/metadata-locking.html
  - MySQL의 메타데이터 락은 5.5 버전부터 생긴 개념으로 MySQL 서버는 메타데이터 락을 통해서 데이터베이스 개체(프로시저, 함수, 테이블, 트리거 등)에 대한 concurrent 요청을 관리하고 데이터 일관성을 보장합니다.
  - 메타데이터 락은 두 경우에 의해 발생됩니다.
    - 개체에 대한 변경작업을 시작할 때, 이 때 생기는 메타데이터 락은 ALTER 를 시도하는 세션을 kill 하면 됩니다.
    - 개체에 대한 변경작업을 마무리할 때, 메타데이터 락을 유발하는 세션을 찾아서 kill 해야하는데 holder session를 찾아야 합니다.
  - performance_schema 에서 threads 테이블과 processlist_info 테이블을 조합해서 어떤 세션이 메타데이터 락을 유발하고 있는지 찾고 kill 하면 됩니다.
    ```sql
    SELECT b.OBJECT_TYPE,
           b.OBJECT_SCHEMA,
           b.OBJECT_NAME,
           b.LOCK_TYPE,
           b.LOCK_STATUS,
           c.THREAD_ID,
           c.PROCESSLIST_ID,
           c.PROCESSLIST_INFO
    FROM information_schema.metadata_locks a
      JOIN performance_schema.metadata_locks b ON a.OWNER_THREAD_ID <> b.OWNER_THREAD_ID
                                                  AND a.OBJECT_NAME = b.OBJECT_NAME
                                                  AND a.LOCK_STATUS = 'PENDING'
      JOIN performance_schema.threads c ON b.OWNER_THREAD_ID = c.THREAD_ID;
    ```
- 참고자료 : https://leezzangmin.tistory.com/51

---

# 8. 성능 개선을 위한 Query 최적화
- MyBatis + MySQL을 사용할 때 비교하는 타입이 다를 경우 반드시 `CAST`를 사용해야 합니다.
  ```sql
    SELECT ...
    FROM ...
    WHERE reference_value1 = CAST(community_id AS CHAR)
  ```
- Admin 화면처럼 검색 조건이 다양하고 검색되는 데이터의 양이 많아서 성능을 조정해야 할 때 Admin 화면처럼 상황에 따라 여러 조건이 있는 경우 조건에 따라 FROM, WHERE 절을 알맞게 조정해주고 검색 쿼리가 복잡한 경우 검색할 데이터의 id 값을 먼저 검색하고 두번째 쿼리에서 `ORDER BY FIELD`를 사용할 수 있습니다.
  ```sql
    SELECT c.community_id AS communityId
    FROM community c
       <if test="bbsName != null and bbsName != ''">
         LEFT JOIN bbs b ON c.bbs_id = b.bbs_id
       </if>
    <where>
       <if test="bbsName != null and bbsName != ''">
         AND b.bbs_name = #{bbsName}
       </if>
    </where> 
  ```
  ```sql
    SELECT c.community_id AS communityId
       <if test="bbsName != null and bbsName != ''">
         b.bbs_name AS bbsName
       </if>
    FROM community c
       <if test="bbsName != null and bbsName != ''">
         LEFT JOIN bbs b ON c.bbs_id = b.bbs_id
       </if>
    WHERE c.community_id IN
          <foreach collection="communityIds" item="id" open="(" separator="," close=")">
             #{id}
          </foreach>
     ORDER BY FIELD(c.community_id,
          <foreach collection="communityIds" item="id" separator=",">
             #{id}
          </foreach>
          )
  ```