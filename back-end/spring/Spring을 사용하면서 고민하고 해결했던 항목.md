<!-- TOC -->
* [Spring](#spring)
  * [Annotation](#annotation)
  * [Spring Handler VO](#spring-handler-vo)
  * [Bean 간 순환 참조 문제](#bean-간-순환-참조-문제)
  * [GET Parameter에서 한글 깨지는 문제](#get-parameter에서-한글-깨지는-문제)
  * [특정 Class와 필드 이름을 입력 받아서 해당 필드 기준으로 값을 정렬하는 함수](#특정-class와-필드-이름을-입력-받아서-해당-필드-기준으로-값을-정렬하는-함수)
  * [특정 날짜가 시작일, 마감일 사이인지 체크하는 함수](#특정-날짜가-시작일-마감일-사이인지-체크하는-함수)
  * [TCP 통신 - IP 단편화](#tcp-통신---ip-단편화)
  * [VO를 상속받고 @EqualsAndHashCOde(callSuper=true)를 주는 이유는?](#vo를-상속받고-equalsandhashcodecallsupertrue를-주는-이유는)
  * [Spring에서 @Autowired보다 생성자 주입 방식을 추천하는 이유는?](#spring에서-autowired보다-생성자-주입-방식을-추천하는-이유는)
  * [순환참조 에러 방지는 애플리케이션 구동 시점에 확인할 수 있따. 스프링 2.6 부터는 알아서 잡아줌.](#순환참조-에러-방지는-애플리케이션-구동-시점에-확인할-수-있따-스프링-26-부터는-알아서-잡아줌)
<!-- TOC -->

---

# Spring

## Annotation
- `@NotNull` : null
- `@NotEmpty` : null, ""
- `@NotBlank` : null, "", " "

## Spring Handler VO
- VO와 DTO를 나눠서 사용하기도 하지만 프로젝트에선 VO만 사용하기로 했다.
- 기본적으로 Front-end와 직접적으로 주고 받는 VO는 ~RequestVO, ~ResponseVO로 명명하고 Request/ResponseVO는 반드시 직접 사용하지 않고 조립해서 사용해야 한다.\
  DB Layer에서 RequestVO를 바로 사용해서도 안되고 SELECT 결과를 바로 ResponseVO로 사용하지 않아야 한다.
- VO는 크게 다음과 같이 나눴다. 테이블과 1대1 매핑되는 `테이블 VO`, Front-end와 통신할 때 사용하는 `RequestVO/ResponseVO`, SELECT 결과를 받는 `Find~VO` (주로 `테이블 VO`를 extends 받아 사용한다.)
- 이 외에 PUT/POST/DELETE에 따라 VO에 Add/Remove/Modify를 붙여서 사용하기도 한다.
- `테이블 VO`을 상속받아서 사용할 땐 @SuperBuilder를 사용한다.
  ```java
  @Data
  @NoArgsConstructor
  @AllArgsConstructor(access = AccessLevel.PRIVATE)
  @SuperBuilder
  public class TableVO { ... }


  @Data
  @NoArgsConstructor
  @EqualsAndHashCode(callSuper = false)
  @AllArgsConstructor(access = AccessLevel.PRIVATE)
  @SuperBuilder
  public class FindTableVO extends TableVO { ... }
  ```
- VO을 만들 때 사용하는 필드 값이 많다면 Builder 패턴을 사용하는 것보다 생성자를 만들어서 처리하는 것이 좋다. Service 단에 코드가 간결해지고 VO에 값에 대한 조립을 생성자에서 처리할 수 있다.
  ```java
  public class TableResponseVO {
      private String name;
      private String title;
      private String city;
      private List<String> hobby;

      public findTableVO(FindTableVO findTableVO) {
          this.name = findTableVO.getName();
          this.title = findTableVO.getTitle();
          this.city = findTableVO.getCity();
          this.hobby = findTableVO.getHobby.stream().map(HobbyResponseVO::new).collect(Collectors.toList());
      }
  }
  ```
- `테이블 VO`에선 다른 VO를 받는 생성자를 사용하면 안되고 다른 VO에서 `toTableVO()` 함수를 만들어서 사용해야 한다.
  ```java
  public class TableRequestVO {
      private String name;
      private String title;
      private String city;

      public TableVO toTableVO() {
          return TableVO.builder()
                          .name(this.name)
                          .title(this.title)
                          .city(this.city)
                          .build();
      }
  }
  ```
## Bean 간 순환 참조 문제
  - Reference : https://www.baeldung.com/circular-dependencies-in-spring
  - Spring Context가 모든 Bean을 로드할 때 일련의 순서로 Bean들을 생성한다.\
    만약에 BeanA -> BeanB -> BeanC 로 참조되어 있다면 Spring은 BacnC를 먼저 생성하고 BeanB를 생성하고 BeanC를 생성한다. 만약 순환 참조가 되어 있다면 Spring은 어떤 Bean을 먼저 생성해야할지 정하지 못한다. 이 때 Spring은 BeanCurrentlyInCreationException을 발생시킨다.
  - 이는 주로 constructor injection을 사용했을 때 발생할 수 있는 케이스이다.
  - 간단한 해결방법으론 @Lazy를 사용해서 생성해주면 된다.
    ```java
    @Component
    public class ClassA {
        private ClassB classB;

        @Autowired
        public ClassA(@Lazy ClassB classB){
            this.classB = classB;
        }
    }
    ```
  - 두번째 방법으론, 생성자 대신에 Setter/Getter를 사용하면 된다.
    ```java
    @Component
    public class ClassA {
        private ClassB classB;

        @Autowired
        public setClassB(ClassB classB){
            this.classB = classB;
        }

        public ClassB getClassB() {
            return classB;
        }
    }
    ```
    ```java
    @Component
    public class ClassB {
        private ClassA classA;

        @Autowired
        public setClassA(ClassA classA){
            this.classA = classA;
        }
    }
    ```
  - 세번째 방법으론 @PostConstruct 를 사용한 방법이다.
    ```java
    @Component
    public class ClassA {
        @Autowired
        private ClassB classB;

        @PostConstruct
        public void init() {
            classB.setClassA(this);
        }

        public ClassB getClassB() {
            return classB;
        }
    }
    ```
    ```java
    @Component
    public class ClassB {
        private ClassA classA;

        public void setClassA(ClassA classA) {
            this.classA = classA;
        }
    }
    ```

## GET Parameter에서 한글 깨지는 문제
- org.springframework.web.util.`UriUtils.encode("String Query", "UTF-8");`
- `uriComponentsBuilder.build(true).encode().toUri()`
  ```java
  public class KakaoServiceImpl {
    public KakaoBlogResponseDTO getKakaoBlog(KakaoBlogRequestDTO kakaoBlogRequestDTO) {
      try {
        HttpHeaders headers = new HttpHeaders();
        headers.add("Authorization", "KakaoAK " + kakaoToken);

        UriComponentsBuilder uriComponentsBuilder = UriComponentsBuilder.fromUriString(kakaoHost + kakaoUrlBlog)
                        .queryParam("sort", kakaoBlogRequestDTO.getSort())
                        .queryParam("page", kakaoBlogRequestDTO.getPage())
                        .queryParam("size", kakaoBlogRequestDTO.getSize())
                        .queryParam("query", UriUtils.encode(kakaoBlogRequestDTO.getQuery(),"UTF-8"));

        HttpEntity<String> kakaoSearchBlogRequest = new HttpEntity<>(headers);
        ResponseEntity<KakaoBlogResponseDTO> kakaoSearchBlogResponse = restTemplate.exchange(
          uriComponentsBuilder.build(true).encode().toUri()
            , HttpMethod.GET
            , kakaoSearchBlogRequest
            , KakaoBlogResponseDTO.class
        );

        if (kakaoSearchBlogResponse.getStatusCode() != HttpStatus.OK)
            throw new ApplicationException("Fail to request Kakao API (" + kakaoUrlBlog + ") : StatusCode is " + kakaoSearchBlogResponse.getStatusCode(), StatusCodeMessageConstant.FAIL);
          else
            return kakaoSearchBlogResponse.getBody();
      } catch (Exception e) {
        throw new ApplicationException("Exception in requesting Kakao API (" + kakaoUrlBlog + ")");
      }
    }
  }
  ```

## 특정 Class와 필드 이름을 입력 받아서 해당 필드 기준으로 값을 정렬하는 함수
```java
public static void sortByFieldName(List target, String fieldName, String orderBy) {
    if (ObjectUtils.isEmpty(fieldName)
            || ObjectUtils.isEmpty(orderBy)
            || ObjectUtils.isEmpty(target)) {
        return;
    }

    Comparator comparator =
            (first, second) -> {
                try {
                    Field field = target.get(0).getClass().getDeclaredField(fieldName);
                    field.setAccessible(true);

                    Comparable firstValue = (Comparable) field.get(first);
                    Comparable secondValue = (Comparable) field.get(second);

                    if (CommonConstants.OrderBy.ASC.name().equalsIgnoreCase(orderBy)) {
                        if (firstValue == null && secondValue == null) {
                            return 0;
                        } else if (firstValue == null) {
                            return 1;
                        } else if (secondValue == null) {
                            return -1;
                        }
                        return firstValue.compareTo(secondValue);
                    } else if (CommonConstants.OrderBy.DESC.name().equalsIgnoreCase(orderBy)) {
                        if (firstValue == null && secondValue == null) {
                            return 0;
                        } else if (firstValue == null) {
                            return -1;
                        } else if (secondValue == null) {
                            return 1;
                        }
                        return secondValue.compareTo(firstValue);
                    } else {
                        return 0;
                    }
                } catch (NoSuchFieldException | IllegalAccessException e) {
                    return 0;
                }
            };
    Collections.sort(target, comparator);
}
```

## 특정 날짜가 시작일, 마감일 사이인지 체크하는 함수
```java
public static boolean isBetweenDateTime(
        Temporal targetDateTime,
        Temporal beginDateTime,
        Temporal endDateTime,
        boolean isIncludeToday) {
    if (Objects.isNull(targetDateTime)
            || Objects.isNull(beginDateTime)
            || Objects.isNull(endDateTime)) {
        return false;
    }

    if (targetDateTime instanceof LocalDate) {
        LocalDate targetDate = (LocalDate) targetDateTime;
        LocalDate beginDate = (LocalDate) beginDateTime;
        LocalDate endDate = (LocalDate) endDateTime;

        if (isIncludeToday) {
            return (targetDate.isAfter(beginDate) || targetDate.isEqual(beginDate))
                    && (targetDate.isBefore(endDate) || targetDate.isEqual(endDate));
        } else {
            return targetDate.isAfter(beginDate) && targetDate.isBefore(endDate);
        }
    } else if (targetDateTime instanceof LocalDateTime) {
        LocalDateTime targetDateAndTime = (LocalDateTime) targetDateTime;
        LocalDateTime beginDateAndTime = (LocalDateTime) beginDateTime;
        LocalDateTime endDateAndTime = (LocalDateTime) endDateTime;

        if (isIncludeToday) {
            return (targetDateAndTime.isAfter(beginDateAndTime)
                            || targetDateAndTime.isEqual(beginDateAndTime))
                    && (targetDateAndTime.isBefore(endDateAndTime)
                            || targetDateAndTime.isEqual(endDateAndTime));
        } else {
            return targetDateAndTime.isAfter(beginDateAndTime)
                    && targetDateAndTime.isBefore(endDateAndTime);
        }
    }
    return false;
}
```

## TCP 통신 - IP 단편화
- [Netty 관련 정리 및 코드](https://github.com/justdoanything/self-study/blob/main/03%20ApplicationModernization.md#netty와-nio)
- 문제상황 : 프로젝트에서 TCP Server/Client를 구축하고 테스트하는 과정에서 로컬에서 Server/Client를 구동해서 테스트하면 잘 됐는데 EKS, EC2 환경에서 하면 패킷이 유실되는 문제가 있었다. 원인은 IP 단편화 때문이었다.
- 원인 : IP fragmentation (단편화)
  - 패킷이 전송될 때 크기가 `MTU(Maximum Transmission Unit)`을 넘어가면 한번에 전송되지 않는다.
  - 각 라우터마다 MTU는 다를 수 있고 MTU가 너무 크면 라우터에 따라 재전송해야 하는 이슈가 있고 너무 작으면 송수신에 오버헤드가 커진다.
  - IP 프로토콜은 network layer의 거의 유일한 프로토콜로 '패킷을 어디로 어떻게 처리할지'를 담당한다. 전송되는 과정중에 fragmentation이 발생하기도 하며, 목적지에 도착하고 나서 재조합된다.
  - `MTU(Maximum Transmission Unit)`
    - MTU는 최대 전송 단위로서 TCP/IP Network 등과 같이 패킷 또는 프레임 기반의 네트워크에서 전송될 수 있는 최대 크기의 패킷 또는 프레임을 가리키며 대개 옥텟을 단위로 사용한다.
    - 4000바이트의 패킷을 전송하려고 하는데 MTU가 1500이다.
    - 패킷은 아래와 같이 분할된다.
      - 각 단편 모두 헤더를 가져야 하기 때문에 최대 1480 Bytes로 분할 될 수 있다.
      - 1번 2번 단편은 뒤에 이어질 단편이 있으므로 More Flag가 1이다.
      - offset은 8 Bytes 단위로 표시된다.

        | No  | Payload | Header | Flag | Offset |
        |-----|---------|--------|------|--------|
        | 1   | 1480    | 20     | 1    | 0      |
        | 2   | 1480    | 20     | 1    | 185    |
        | 3   | 1020    | 20     | 0    | 370    |
- 해결 : 구현했던 Decode 부분을 수정해서 IP 단편화 문제를 해결했다.
```java
@Override
protected void decode(ChannelHandlerContext ctx, ByteBuf in, List<Object> out) throws Exception {
    ByteBuf copyBuf = in.duplicate();
    String commandString = readString(copyBuf);
    int packetLength = 0;
    if(commandString.length() > 6){
        packetLength = Integer.parseInt(commandString.substring(0,6));
    }else{
        return;
    }

    if (in.readableBytes() == packetLength) {
        out.add(in.readBytes(in.readableBytes()));
    }else{
        return;
    }
}

public String readString(ByteBuf buf) {
    String result = null;
    String charSet = "EUC_KR";
    int bytes = buf.bytesBefore((byte) 0);
    if (bytes == -1) {
        bytes = buf.readableBytes();
        result= buf.toString(buf.readerIndex(), bytes, Charset.forName(charSet));
        buf.skipBytes(bytes);
    } else {
        result = buf.toString(buf.readerIndex(), bytes, Charset.forName(charSet));
        buf.skipBytes(bytes + 1);
    }
    return result;
}
```

## VO를 상속받고 @EqualsAndHashCOde(callSuper=true)를 주는 이유는?
callSuper=false일 경우, eqauls()와 hashCode()에서 상속받은 VO의 필드를 고려하지 않게 된다.
따라서 상속 받은 VO의 필드값이 달라도 같은 VO라는 결과가 나올 수 있다. 이를 방지하지 위해서 @EqualsAndHashCode(callSuper = true)를 줘서 eqauls()와 hashCode() 비교 시 부모의 필드 값까지 같은지 비교하도록 해야 한다.

## Spring에서 @Autowired보다 생성자 주입 방식을 추천하는 이유는?
주입받은 객체가 변하지 않는다는 불변성을 보장
@Autowired를 사용하면 외부에서 접근이 불가능하기 때문에 테스트 코드 짜는데 불편하다. 테스트 코드가 특정 프레임워크에 종속적이면 좋지 않다. @Autowired는 Spring에 종속적이지만 생성자 주입은 순수 자바 코드로 테스트 코드 작성이 가능하다.
테스트 코드가 Spring 위에서 동작하지 않을 경우 @Autowired로 주입한 객체는 null이 되고 이는 런타임 시점에 에러로 발견된다.
반면에 생성자 주입은 컴파일 시점에 객체를 주입받아 테스트 코드를 작성할 수 있다. 객체가 누락될 경우 컴파일 시점에 에러를 확인할 수 있다.

생성자 주입이 필요한 경우 final 키워드를 사용할 수 있고 이는 lombok에 @RequiredArgsConstructor을 쓰면 코드가 간결해진다.

@Autowired를 사용하면 Spring의 의존성이 침투되는 것이다. 언제라도 Spring이 변해서 @Autowired에 기능이나 뭔가 바뀐다면 내 코드가 영향을 받게 된다.

순환참조 에러 방지는 애플리케이션 구동 시점에 확인할 수 있따. 스프링 2.6 부터는 알아서 잡아줌.
---

