<!-- TOC -->
* [AWS ECS and EC2](#aws-ecs-and-ec2)
* [Amazon Cognito](#amazon-cognito)
* [Amazon Connect](#amazon-connect)
* [Amazon Lambda](#amazon-lambda)
<!-- TOC -->

---

# AWS ECS and EC2
- ## ECS (Elastic Container Service)
  - AWS 카테고리로 봤을 땐 `Container Orchestration`에 속한다.
  - Cluster에서 Container를 손쉽게 실행, 중지 및 관리할 수 있게 해주는 컨테이너 관리 서비스 
  - 간단한 API 호출을 통해서 Container 기반의 Application을 제어할 수 있습니다.
  - 리소스, 격리 정책, 가용성 요구사항을 기반으로 Cluster에 Container 배치를 예약할 수 있습니다.
  - 일관된 배포 및 구축 환경을 생성할 수 있고 배치 및 ETL(Extract-Transform-Load) 워크로드를 관리할 수 있습니다.
  - VPC 내에 Amazon ECS Cluster를 생성할 수 있고 Coinater Image는 ECR(Elastic Container Registry)에 저장됩니다.
  - `Task Definition` : JSON 타입의 파일로 사용할 자원, 실행할 container 개수 등을 정의한다. 여러개의 Task를 정의해서 Application을 시작할 때 시작시킬 수 있다.
  - `Task` and `Scheduling` : Task는 Cluster 내에서 Task Definition 기반으로 수행되는 인스턴스입니다. Scheduling은 cluster 내에서 수행할 task를 관리한다.
  - `ECS Cluster` : ECS Cluster는 Task 또는 Service의 논리적 그룹입니다. Cluster에 1개 이상의 EC2를 등록하고 여기에서 Task를 실행할 수 있습니다. 
  - `Container Agent` : ECS Cluster 내에 있는 Container 안에서 실행되고 Resource 사용률에 대한 정보를 ECS 로 전송합니다. Agent는 ECS로부터 요청을 받을때마다 시작되거나 중지된다.
  ![image](https://user-images.githubusercontent.com/21374902/150706403-5c721f9c-5b06-412d-8018-e46c3fffe862.png)

- ## EC2 (Elastic Compute Cloud)
  - AWS 카테고리로 봤을 땐 `Computing Option` 이고 `Cloud Computing Service` 라고도 불린다.
  - AWS 내에 컴퓨터 1개를 구성하는 것과 비슷한 개념으로 AWS가 제공하는 URL(Public DNS)을 통해 이 컴퓨터에 접근할 수 있다. (=> 기존에는 H/W를 구매해서 `컴퓨터`를 구성했지만 AWS에선 클릭 몇번으로 더 쉽게 구매와 설정이 가능해지도록 만들었다. )
  - 사용한만큼 비용을 내며 생성/삭제가 아주 간단하다.
  - SSH를 통해 외부에서 이 컴퓨터에 연결할 수 있다.
  - 보안, 네트워킹 그리고 스토리지 관리에 유용하다.
  - EC2가 제공하는 기능
    - AMI (Amazon Machine Image) : 서버에 필요한 운영체제와 여러 소프트웨어들이 적절히 구성된 상태로 제공되는 템플릿으로 인스턴스를 쉽게 만들 수 있다.
    - CPU, Memory, Storage, Network 등 여러 종류의 구성을 제공
    - Key Pair를 사용하여 인스턴스 로그인 정보 보호
    - Instance Store Volumn : 임시 데이터를 저장하는 스토리지 볼륨으로 인스턴스 중단, 최대 절전 모드로 전환 또는 종료 시 삭제됨
    - EBS (Elastic Block Store)를 사용해서 영구 Storage에 데이터 저장 가능

- ## Reference
  - https://docs.aws.amazon.com/ko_kr/AmazonECS/latest/developerguide/Welcome.html
  - https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/concepts.html
  - https://d1.awsstatic.com/training-and-certification/docs-sa-assoc/AWS-Certified-Solutions-Architect-Associate_Exam-Guide.pdf

---

# Amazon Cognito
- 웹 및 모바일 앱에 대한 인증, 권한 부여 및 사용자 관리를 제공합니다. 사용자는 사용자 이름과 암호를 사용하여 직접 로그인하거나 Facebook, Amazon, Google 또는 Apple 같은 타사를 통해 로그인할 수 있습니다.  
  <img width="411" alt="image" src="https://user-images.githubusercontent.com/21374902/193582302-89b9f50b-22a7-48d4-8edb-f8ac91b805ee.png">

- ## 사용자 흐름
  - 첫 번째 단계에서 앱 사용자는 `사용자 풀`을 통해 로그인하여 인증 성공 이후 사용자 풀 토큰을 받습니다.
  - 다음으로 앱은 `자격 증명 풀`을 통해 사용자 풀 토큰을 AWS 자격 증명으로 교환합니다.
  - 마지막으로 앱 사용자는 AWS 자격 증명을 사용하여 Amazon S3, DynamoDB 등 다른 AWS 서비스에 액세스할 수 있습니다.

- ## 사용자 풀(User Pool)
  - 가입 및 로그인, 소셜 로그인, 사용자 정보 관리, 2FA 등 기능을 제공
  - 사용자 풀 인증 흐름  
    <img width="520" alt="image" src="https://user-images.githubusercontent.com/21374902/193584060-1e3b492b-74da-4771-ae4e-b1623dea7613.png">
- ## Lambda를 통한 Customize
  - Cognito를 사용할 때 특정 시점에 원하는 trigger 등과 같은 customize를 정의할 때 사용할 수 있습니다.
  - https://docs.aws.amazon.com/ko_kr/cognito/latest/developerguide/cognito-user-identity-pools-working-with-aws-lambda-triggers.html

    | 사용자 풀 흐름            | 작업                    | 설명                                       |
    |---------------------|-----------------------|------------------------------------------|
    | 사용자 지정 인증 흐름        | 인증 문제 정의              | 사용자 지정 인증 흐름에서 다음 문제를 결정합니다.             |
    | 사용자 지정 인증 흐름        | 인증 문제 생성              | 사용자 지정 인증 흐름에서 다음 문제를 생성합니다.             |
    | 사용자 지정 인증 흐름        | 인증 문제 응답 확인           | 사용자 지정 인증 흐름에서 응답이 올바른지 결정합니다.           |
    | 인증 이벤트              | 사전 인증 Lambda 트리거      | 로그인 요청 승인 및 거절에 대한 사용자 지정 검증입니다.         |
    | 인증 이벤트              | 사후 인증 Lambda 트리거      | 사용자 지정 분석을 위한 이벤트 기록                     |
    | 인증 이벤트              | 사전 토큰 생성 Lambda 트리거   | 토큰 신청 증강 또는 억제                           |
    | 가입                  | 사전 가입 Lambda 트리거      | 가입 요청을 수락 또는 거부하는 사용자 지정 검증 수행           |
    | 가입                  | 사후 확인 Lambda 트리거      | 사용자 지정 분석을 위한 사용자 지정 시작 메시지 또는 이벤트 로깅 추가 |
    | 가입                  | 사용자 마이그레이션 Lambda 트리거 | 기존 사용자 디렉터리에 있는 사용자를 사용자 풀로 마이그레이션       |
    | 메시지                 | 사용자 정의 메시지 Lambda 트리거 | 고급 사용자 지정 및 메시지 현지화 수행                   |
    | 토큰 생성               | 사전 토큰 생성 Lambda 트리거   | ID 토큰 속성 추가 또는 제거                        |
    | 이메일 및 SMS 서드 파티 공급자 | 사용자 지정 발신자 Lambda 트리거 | 서드 파티 공급자를 사용하여 SMS 및 이메일 메시지 보내기        |

- ## 3rd party 로그인
  
  <img width="718" alt="image" src="https://user-images.githubusercontent.com/21374902/193587955-dce3309e-351d-4ebc-aee0-6c9ae9d4a58a.png">

  <img width="718" alt="image" src="https://user-images.githubusercontent.com/21374902/193588007-410b62b1-2f01-4a43-a1b3-68b69e40b8ec.png">

- ## Sequence Diagram
  - ### 회원가입  
    ![Untitled-2](https://user-images.githubusercontent.com/21374902/194447273-4da943ca-c310-4ce5-8f11-40523d04102b.png)

  - ### 로그인  
    ![Untitled](https://user-images.githubusercontent.com/21374902/194447086-53d3d0b7-2605-4641-a2c8-6b5a93d865e2.png)

  - ### 로그아웃 및 회원탈퇴  
    ![Untitled-3](https://user-images.githubusercontent.com/21374902/194447263-d9e1c057-996e-40a0-ae7f-087ecc1cb909.png)

- ## Reference
  - https://docs.aws.amazon.com/ko_kr/cognito/latest/developerguide/what-is-amazon-cognito.html
  - https://velog.io/@w1nu/쉽게-풀어쓴-AWS-Cognito-기초-이론

---

# Amazon Connect
- ## 1. 서비스 개요
  - 간단히 말하면 고객센터를 구축해주는 Amazon의 몇 안되는 `완성형 서비스`이다. 간단한 [데모 영상](https://www.youtube.com/watch?v=wnmXSqHlgyM)을 보고 따라해봤을 때 인스턴스를 구축하고 상담원과 전화 연결을 하는데 10분이 채 걸리지 않았다.
  - Amazon Connect에서 제공하는 주요 서비스는 아래와 같다.  

    | 주요 서비스                                             | 설명                                        |
    |----------------------------------------------------|-------------------------------------------|
    | [Amazon Polly](https://aws.amazon.com/ko/polly/)   | TTS(Text-to-speech) 서비스 제공                |
    | [Amazon Lex](https://aws.amazon.com/ko/lex/)       | AI Chatbot 서비스 제공                         |
    | [Softphone](https://github.com/aws/connect-rtc-js) | 별도의 작업 없이 바로 상담원이 전화를 받고 걸 수 있는 서비스 제공    |
    | [Amazon Lambda](https://aws.amazon.com/ko/lambda/) | 원하는 비지니스 로직을 Contact Flow에 적용할 수 있는 서비스   |
    | Contact Flow                                       | 고객 응대 흐름을 Diagram 형태로 바로 만들고 수정할 수 있는 서비스 |


- ## 2. 서비스 특징
  - AWS 공식 페이지에선 아래와 같이 설명을 하고 있다.
    - 클릭 몇 번으로 클라우드 고객 센터를 설정하고 에이전트를 온보딩하여 고객을 즉시 지원할 수 있다.
    - 올인원 형태의 AI 및 ML 기반 고객 센터를 통해 음성 및 디지털 채널 전반에서 에이전트 생산성과 고객 경험을 개선할 수 있습니다.
    - 위치에 관계없이 수만 명에 이르는 에이전트를 온보딩할 수 있는 유연성을 통해 고객 수요에 따라 간편하게 확장 또는 축소할 수 있습니다.
    - 기존 고객 센터 솔루션과 비교하여 최소 비용, 장기 약정 또는 선결제 라이선스 요금 없이 최대 80%를 절약할 수 있습니다.
  - 인스턴스 구축에 필요한 대부분의 기능을 제공하기 때문에 클릭 몇 번으로 기본적인 인스턴스를 바로 만들고 운영할 수 있다.
  - Amazon에서 기본적으로 제공하는 고객센터 번호를 바로 사용할 수 있다. _(수신자 부담, 발신자 부담 설정 가능)_
  - 계정 관리를 통해 Admin, Manager, Caller를 설정할 수 있고 [Softphone](https://github.com/aws/connect-rtc-js), [AI Chatbot(Amazon Lex)](https://aws.amazon.com/ko/lex/)도 같이 제공한다. _(Multi Channel)_
  - 고객 응대 흐름은 draw.io에서 diagram 그리듯 그릴 수 있어서 편리하다. [AI Chatbot(Amazon Lex)](https://aws.amazon.com/ko/lex/)에 대한 흐름도 그릴 수 있다.
  - [Softphone](https://github.com/aws/connect-rtc-js)을 제공하기 때문에 인터넷 연결만 있으면 별도의 하드웨어(장비, 전화기 등) 없이 관리자와 상담원은 어디서든 사용할 수 있다. _(AutoScailing)_
  - 이벤트 기간 등 트래픽이 몰리는 상황에서 시스템을 증설 및 축소를 간단하게 할 수 있다.
  - [Amazon Polly](https://aws.amazon.com/ko/polly/)를 사용해 TTS(Text-To-Speech) 기능을 사용할 수 있다. 타이핑을 통해 안내 멘트를 간단하게 만들 수 있다. _(한국어 지원)_
  - [Amazon Lambda](https://aws.amazon.com/ko/lambda/)를 통해서 원하는 비지니스 로직을 부여할 수 있다. 예를들어 비행기를 취소 요청한 고객이 전화했을 때 "취소한 여행편이 있습니다. 이에 관련한 문의신가요?" 라는 질문을 먼저 할 수 있다. _(동적 반응)_
  - 통화, 상담 내용은 자동으로 암호화해서 저장하고 다시 들을 수 있다. _(KMS 암호화 방식)_
  - 저장된 데이터들을 적재하고 빅데이터를 기반으로 여러 서비스를 만들 수 있다.
  - 다양한 언어를 지원하기 때문에 Global 통합 운영 센터를 빠르게 만들 수 있다.
  - [데모 영상](https://www.youtube.com/watch?v=wnmXSqHlgyM)에서 소개하는 특징은 아래와 같다.  
    <img width="735" alt="스크린샷 2023-01-26 13 46 23" src="https://user-images.githubusercontent.com/21374902/215044938-2e5cf32e-d370-4b98-b408-ba0253cb2281.png">  
    <img width="736" alt="스크린샷 2023-01-26 14 07 05" src="https://user-images.githubusercontent.com/21374902/215044948-193f7242-2e80-446e-8502-e274457eae4e.png">  
    <img width="731" alt="스크린샷 2023-01-26 14 06 58" src="https://user-images.githubusercontent.com/21374902/215044944-5b9c3e68-ccbc-4ffd-bbdf-9161b8137527.png">  
    <img width="736" alt="스크린샷 2023-01-26 15 25 31" src="https://user-images.githubusercontent.com/21374902/215044952-fe53947e-7db3-4e9c-b46e-5e3942862600.png">  

- ## 3. 기대 효과
  - 원하는 비지니스에 대한 기능은 [Amazon Lambda](https://aws.amazon.com/ko/lambda/) 통해 사용할 수 있고 같은 AWS Infra에 있다면 AWS Resource와 연계하여 사용하기에도 용이해보인다.
  - 무엇보다 장비를 사거나 도메인을 할당받고 전화번호를 신청하고 그런 과정 없이 클릭 몇 번으로 고객센터를 바로 만들 수 있는게 큰 장점인 것 같다.
  - 기본으로 제공되는 [Softphone](https://github.com/aws/connect-rtc-js), [AI Chatbot(Amazon Lex)](https://aws.amazon.com/ko/lex/), [Amazon Polly](https://aws.amazon.com/ko/polly/) 기능이 좋아서 상담원만 있으면 전화를 수신하고 상담할 수 있는 업무는 바로 시작할 수 있다.
  - Contact Flow를 diagram으로 바로 만들 수 있는 것도 큰 장점이다.
  - Multi Channel에서 수집되고 저장되는 빅데이터를 활용해서 다른 서비스를 만들 수 있다.
  - [데모 영상](https://www.youtube.com/watch?v=wnmXSqHlgyM)에서 소개하는 기대효과는 아래와 같다.  
    <img width="734" alt="스크린샷 2023-01-26 15 27 18" src="https://user-images.githubusercontent.com/21374902/215044954-01ae0d8e-2787-4d00-bf81-8f42f1a6ed14.png">  
    <img width="736" alt="스크린샷 2023-01-26 15 27 42" src="https://user-images.githubusercontent.com/21374902/215044955-545de8b3-c026-4ec5-9b88-d5e5d313df7f.png">  
    <img width="735" alt="스크린샷 2023-01-26 15 28 27" src="https://user-images.githubusercontent.com/21374902/215044956-29196c44-1330-453a-8826-6e2a1c960db0.png">  
    <img width="734" alt="스크린샷 2023-01-26 15 32 05" src="https://user-images.githubusercontent.com/21374902/215044961-d78a3f27-cc47-4819-a8c5-178e1dc31b02.png">  

- ## 4. 대표 구현 사례
  - [Priceline - 통화량이 3배 증가하는 중에 고객 서비스를 최적화](https://aws.amazon.com/ko/solutions/case-studies/priceline/?did=cr_card&trk=cr_card)
  - [Deliveroo - AWS를 사용하여 고객의 기대치에 부응](https://pages.awscloud.com/rs/112-TZM-766/images/AWS%20Deliveroo%20Innovator.pdf)
  - [Unum - 완전히 새로운 보험 고객 경험 재정립](https://aws.amazon.com/ko/solutions/case-studies/unum-video/)

- ## 5. 기타 참고 자료
  - [Amazon Connect](https://docs.aws.amazon.com/connect/index.html)
  - [Amazon Lex](https://aws.amazon.com/ko/lex/)
  - [Amazon Polly](https://aws.amazon.com/ko/polly/)
  - [Amazon Lambda](https://aws.amazon.com/ko/lambda/)
  - [Softphone](https://github.com/aws/connect-rtc-js)
  - [Amazon Connect Admin Guide](https://docs.aws.amazon.com/ko_kr/connect/latest/adminguide/what-is-amazon-connect.html)
  - [Amazon Connect Lambda Guide](https://docs.aws.amazon.com/ko_kr/connect/latest/adminguide/connect-lambda-functions.html)

- ## 6. Amazon Connect 만들어보기
  - ### (1) Instance 구축
    - [Amazon Connect](https://docs.aws.amazon.com/connect/index.html) 접속 → Amazon Connect 시작하기 → Create Instance    
    <img width="600" height="484" alt="스크린샷 2023-01-27 10 07 48" src="https://user-images.githubusercontent.com/21374902/215044965-02c2bd74-946e-444a-a375-b77c6d460625.png">  
    <img width="300" alt="스크린샷 2023-01-27 10 20 03" src="https://user-images.githubusercontent.com/21374902/215044987-a8852510-79ea-4a52-9789-9b7fee63a9b4.png">  

  - ### (2) Amazon Connect Dashboard  
    <img width="600" alt="스크린샷 2023-01-27 10 20 44" src="https://user-images.githubusercontent.com/21374902/215044989-d8e86809-4942-4d45-b99e-0db871bc1235.png">

  - ### (3) Contact Flow
    - ⭐️ 중요 포인트 ⭐
      - TTS 기본 언어는 English로 되어 있기 때문에 한국어로 변경해줘야 한다. Flow 시작 부분에 `Set Voice` = Korean 으로 설정해야 한다. 
      - Lambda 함수 응답은 JSON 형태의 depth가 1이고 value는 기본 타입을 사용해야한다. value에 list, json 등이 오면 `Invoke AWS Lambda Function`에서 Error로 판단한다.
      - Play prompt 등에서 Flow 내 한국어로 된 변수(Lambda 응답, Contact Attribute 등)를 사용하려면 Type은 반드시 Text 이여야 한다. SSML은 한국어 변수를 제대로 인식하지 못한다. 
    - 개발적인 요소가 들어가는 곳은 Contact Flow에서 `Invoke AWS Lambda Function`에서 Lambda를 개발하고 변수를 활용하는 부분이다.
    - 1) Invoke AWS Lambda Function
      - Document : [Amazon Connect Lambda 활용 가이드](https://docs.aws.amazon.com/ko_kr/connect/latest/adminguide/connect-lambda-functions.html)
      - Lambda 작성하기 ([Amazon Lambda](#amazon-lambda-1) 참고)
          - Instance에 Lambda 추가하기
            - Amazon Connect Console → 원하는 Instance의 alias 선택 → AWS Lambda 영역에서 Lambda 작성 또는 만들어둔 Lambda 선택
          - Contact Flow에 Lambda 활용하기
            - Lambda에 Input Parameter 부여하기
              - Input Parameter로 넘기는 방법은 크게 2가지로, `Contact Attribute`로 설정해서 넘기는 방법과 `Invoke AWS Lambda Function`에서 직접 부여하는 방법이 있다.
              - `Contact Attribute` → Add another attribute → User defined로 임의의 값을 설정하거나 System value 사용  
                <img width="500" alt="스크린샷 2023-01-31 16 39 07" src="https://user-images.githubusercontent.com/21374902/215784146-f924f1e5-4a54-4d30-bc0c-6d7fbc1fc6f4.png">
              
              - `Invoke AWS Lambda Function` → Add a parameter → User defined로 임의의 값을 설정하거나 System value 사용  
                <img width="500" alt="스크린샷 2023-01-31 16 41 20" src="https://user-images.githubusercontent.com/21374902/215784157-af7f3f5c-e111-4e8e-a05b-3e89084024af.png">
            - Lambda에서 Input Parameter 사용하기
              - `Contact Attribute`로 설정한 값 : Details.ContactData.Attributes.contactName;
              - `Invoke AWS Lambda Function`로 설정한 값 : Details.Parameters.nickname;
              - Lambda Test를 할 때 amazonConnect 샘플을 보면 Input 데이터 형식을 볼 수 있다.
                ```json
                {
                  "Name": "ContactFlowEvent",
                  "Details": {
                    "ContactData": {
                      "Attributes": {},
                      "Channel": "VOICE",
                      "ContactId": "5ca32fbd-8f92-46af-92a5-6b0f970f0efe",
                      "CustomerEndpoint": {
                        "Address": "+11234567890",
                        "Type": "TELEPHONE_NUMBER"
                      },
                      "InitialContactId": "5ca32fbd-8f92-46af-92a5-6b0f970f0efe",
                      "InitiationMethod": "API",
                      "InstanceARN": "arn:aws:connect:us-east-1:123456789012:instance/9308c2a1-9bc6-4cea-8290-6c0b4a6d38fa",
                      "MediaStreams": {
                        "Customer": {
                          "Audio": {
                            "StartFragmentNumber": "91343852333181432392682062622220590765191907586",
                            "StartTimestamp": "1565781909613",
                            "StreamARN": "arn:aws:kinesisvideo:us-east-1:123456789012:stream/connect-contact-a3d73b84-ce0e-479a-a9dc-5637c9d30ac9/1565272947806"
                          }
                        }
                      },
                      "PreviousContactId": "5ca32fbd-8f92-46af-92a5-6b0f970f0efe",
                      "Queue": null,
                      "SystemEndpoint": {
                        "Address": "+11234567890",
                        "Type": "TELEPHONE_NUMBER"
                      }
                    },
                    "Parameters": {}
                  }
                }              
                ```         
            - Lambda의 Response를 Flow에서 사용하기
              - `$.` 문자로 변수를 사용할 수 있다.
              - 한국어를 사용할 경우 SSML은 사용할 수 없기 때문에 <say-as>, <speak> 태그는 사용하지 않아야 하고 interpret as = Text로 설정하고 변수를 바로 사용해야 한다. (예시 : `$.External.nickname`)  
                <img width="500" alt="스크린샷 2023-01-31 16 39 32" src="https://user-images.githubusercontent.com/21374902/215784926-429b755e-6da6-4efd-8924-8cc6ef34bbca.png">  
                <img width="500" alt="스크린샷 2023-01-31 16 41 58" src="https://user-images.githubusercontent.com/21374902/215785100-32f6fab8-336a-442e-8c44-7441461dc401.png">

---

# Amazon Lambda

- ## 1. 기본개념 
  - ### (1) 목표 : Amazon Lambda를 로컬 환경에서 개발하고 테스트할 수 있다.

    | OS    | IDE               | Language   |
    |-------|-------------------|------------|
    | macOS | Intellij Ultimate | Java 11    |
    | macOS | Intellij Ultimate | Typescript |

  - ### (2) Java와 Nodejs 중 어떤 언어로 개발해야할까?
    - `ORM을 사용한 Java`는 GET은 제일 느리고 POST는 가장 빠르다. 
    - `ORM을 사용하지 않은 Java`는 GET이 가장 빠르고 POST는 가장 느리다. 
    - `Nodejs`는 그 중간값을 가진다.  
      <img width="631" alt="image" src="https://user-images.githubusercontent.com/21374902/228151976-ccb406ec-0861-4f82-85d9-baedeee6dde5.png">
  - ### (3) Cold Start 문제와 SnapStart
    - Java는 `Cold Start` 문제를 갖고 있다. `Cold Start`란 함수가 처음 실행될 때 함수 코드와 런타임 환경을 초기화하는 과정이다. 코드가 배포되면 새로운 컨테이너가 생성되고 최초 실행 시 초기화 과정인 `Cold Start` 시간이 필요한 것이다. 이후에 호출될 때는 초기화 시간이 필요하지 않지만 Lambda는 일정 시간 동안 호출되지 않으면 삭제되기 때문에 언제 다시 `Cold Start` 시간이 필요할지 모른다. 이를 해결하기 위해서 Lambda에 할당하는 메모리 크기를 늘려서 초기화 시간을 단축하거나 Previsioned Concurrency를 사용하는 방법이 있다.
      - `Cold Start` 문제를 해결하기 위해 AWS는 `SnapStart`라는 기능을 개발했다.
      - Lambda의 수명주기는 아래와 같다.
        - init
          - 함수의 runtime을 bootstrap하고 정적 코드를 실행하는 과정
          - 대부분의 경우 밀리초 안으로 완료되지만 SpringBoot, Quarkus, Micronual과 같은 Framework와 함께 Java Runtime 중 하나를 사용하는 Lambda 함수의 init 시간은 10초 이상이 소요될 수 있다.
          - 종속성 삽입, 함수 코드 컴파일, Class Path 구성 요소 스캔 등에 시간이 소요되기 떄문이다.
          - 다른 이유로는 정적 코드에서 일부 기계 학습 모델을 다운로드하거나 일부 참조 데이터를 미리 계산하거나 다른 AWS 서비스에 대한 네트워크 연결을 설정할 때에도 시간이 오래 소요될 수 있다. 
        - invoke
        - shutdown
    - SnapStart 동작 방식
      - 특정 Lambda에서 SnapStart를 활성화하고 새 버전을 게시하면 최적화된 프로세스가 실행된다.
      - init 단계를 수행하고 메모리 및 디스크 상태의 변경 불가능한 암호화된 스냅샷을 가져와서 다시 사용할 수 있도록 캐싱한다.
      - 이후에 함수가 호출되면 상태에 따라 캐시에서 청크 단위로 검색되어 실행 환경을 채우는데 사용된다.
      - 새로운 환경을 만드는데 init 단계가 필요하지 않게 되고 최적화를 통해 호출 시간이 단축된다.
    - SnapStart 필요 조건
      - Java 11 이상
      - 미국 동부(오하이오, 버지니아 북부), 미국 서부(오레곤), 아시아 태평양(싱가포르, 시드니, 도쿄) 및 유럽(프랑크푸르트, 아일랜드, 스톡홀름) 리전에서 사용 가능
    - SnapStart 장점

      | 장점          | 설명                                                                                                                                                                                                                                                                                                                        |
      |-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
      | 스냅 복원력 강화   | Lambda SnapStart는 초기화된 단일 스냅샷을 재사용하여 여러 실행 환경을 재개함으로써 애플리케이션 속도를 높입니다. 이는 코드에 몇 가지 흥미로운 영향을 미칩니다.                                                                                                                                                                                                                         |
      | 고유성         | napStart를 사용하는 경우 고유성을 유지하려면 초기화 중에 생성되었던 모든 고유 컨텐츠를 초기화 후에 생성해야 합니다. 사용자(또는 사용자가 참조하는 라이브러리)가 유사 난수 생성기를 사용하는 경우 Init 단계에서 얻은 시드를 기반으로 해서는 안됩니다. SnapStart와 함께 사용할 때 임의성을 보장하기 위해 OpenSSL의 Rand_bytes를 업데이트했고java.security.SecureRandom이 이미 스냅 복원력이 있음을 확인했습니다. Amazon Linux의 /dev/random 및 /dev/urandom도 스냅 복원력이 뛰어납니다. |
      | 네트워크 연결     | 코드가 Init 단계에서 네트워크 서비스에 대한 장기 연결을 만들어 Invoke 단계에서 사용하는 경우 필요 시 연결을 다시 설정할 수 있는지 확인하세요. 이를 위해 AWS SDK가 이미 업데이트되었습니다.                                                                                                                                                                                                       |
      | 임시 데이터      | 이는 사실상 위 항목의 보다 일반적인 형태입니다. Init 단계에서 코드가 참조 정보를 다운로드하거나 계산하는 경우 캐싱 기간 동안 참조 정보가 오래되지 않았는지 빠르게 확인하는 것이 좋습니다.                                                                                                                                                                                                              |
      | 캐싱          | 캐싱된 스냅샷은 14일 동안 사용하지 않으면 제거됩니다. 업데이트되거나 패치된 런타임에 따라 스냅샷이 달라지는 경우 Lambda는 자동으로 캐시를 새로 고칩니다.                                                                                                                                                                                                                                |
      | 요금          | Lambda SnapStart를 사용하는 데는 추가 요금이 부과되지 않습니다.                                                                                                                                                                                                                                                                               |
      | 기능 호환성      | 더 큰 임시 스토리지, Elastic File Systems, Provisioned Concurrency 또는 Graviton2에서는 Lambda SnapStart를 사용할 수 없습니다. 일반적으로 범용 Lambda 함수에는 SnapStart를 사용하고 지연 시간에 매우 민감한 함수의 하위 집합에는 Provisioned Concurrency를 사용하는 것이 좋습니다.                                                                                                            |
      | Firecracker | 이 기능은 Firecracker 스냅샷 생성을 사용합니다.                                                                                                                                                                                                                                                                                          |

- ## 2. 실습예제
  - ### (1) 공통 : AWS SAM CLI 설치
    - `brew tap aws/tap`
    - `brew install aws-sam-cli`
    - `sam --version`
    - `brew update aws-sam-cli`

  - ### (2) Java
    - #### 1) Intellij에 AWS Toolkit 설치  
      - Preferences -> Plugins -> Martketplace -> AWS Toolkit 설치

    - #### 2) Serverless 프로젝트 생성  
      - File -> New -> Project -> AWS -> AWS Serverless Application
      - `Validation of sam failed: Not installed.` 에러가 뜰 경우, SAM CLI executable에 `which sam` 해서 나온 경로 입력
    
    - #### 3) Build & Test with SAM  
      - ⭐️ Docker를 Rancher로 돌리는 경우엔 sam 사용이 불가합니다. Docker Desktop을 설치하거나 AWS Lambda Console에 올려서 테스트 해야 합니다.
      - Intellij
        - Run Configuration에서 input, aws profile 등 설정하고 실행
      - Command
        - SAM 구동 후 테스트 : `sam local start-api` -> 원하는 URL 호출
        - 직접 호출하는 방법 : `sam local invoke "HelloWorldFunction" -e events/event.json`
    
    - #### 4) 함수 개발  
      - ⭐ 중요 포인트 ⭐
        - 함수의 응답은 String 타입이 아닌 POJO 형식의 Class를 반환해야 한다. 응답을 String 타입의 JSON 형태로 하면 __"{\\"name\\":\\"beaver\\"}"__ 처럼 큰 따움표가 붙는다. POJO 형식의 Class를 반환하면 Lambda에서 알아서 Serialize를 해준다.
        - 함수는 반드시 RequestHandler Class를 상속 받아야 하고 Input/Output Parameter의 형식을 지정할 수 있다.
        - Input으로 사용하는 event.json과 같은 형식은 AWS에 Sample이 많이 있으므로 참조해서 사용한다.
    
      ```java
      public class ScanTable implements RequestHandler<Object, ScanTableResponse> {
    
        private final Region REGION = Region.AP_NORTHEAST_2;
        private final String tableName = "sampleTable";
    
        private final Gson gson = new GsonBuilder().setPrettyPrinting().create();
        private final DynamoDbClient dynamoDbClient = DynamoDbClient.builder().region(REGION).build();
    
        @Override
        public ScanTableResponse handleRequest(Object input, Context context) {
            JsonObject json = gson.toJsonTree(input).getAsJsonObject();
            String nickname = json.getAsJsonObject("Details").getAsJsonObject("Parameters").get("nickname").getAsString();
            String contactName = json.getAsJsonObject("Details").getAsJsonObject("ContactData").getAsJsonObject("Attributes").get("contactName").getAsString();
            String phoneNumber = json.getAsJsonObject("Details").getAsJsonObject("ContactData").getAsJsonObject("CustomerEndpoint").get("Address").getAsString();
    
            Map<String, AttributeValue> where = new HashMap<>();
            where.put(":phoneNumber", AttributeValue.builder().s(phoneNumber).build());
    
            ScanRequest scanRequest = ScanRequest.builder()
                    .tableName(tableName)
                    .expressionAttributeValues(where)
                    .filterExpression("phoneNumber = :phoneNumber")
                    .build();
    
            try{
                ScanResponse response = dynamoDbClient.scan(scanRequest);
                return ScanTableResponse.builder()
                            .isReserved(true)
                            .nickname(nickname)
                            .contactName(contactName)
                            .phoneNumber(phoneNumber)
                            .flightNo(response.items().get(0).get("flightNo").s())
                            .build();
            }catch (Exception e){
                System.out.println("Logger Exception : " + e.toString());
                return ScanTableResponse.builder()
                        .isReserved(false)
                        .nickname(nickname)
                        .contactName(contactName)
                        .phoneNumber(phoneNumber)
                        .build();
            }
        }
      }   
      ```
    
    - #### 5) Lmabda 테스트
      - Lambda에 Jar 업로드하기
        - build.gradle에 task 추가
          ```yaml
          task buildZip(type: Zip) {
            from compileJava
            from processResources
            into('lib') {
              from configurations.runtimeClasspath
            }
          }
          ```
        - gradle에서 buildZip 실행
        - 생성된 Jar 파일을 AWS Lambda Console에 업로드
        - 아래 사진과 같이 Lambda Console에서 실행시킬 함수의 경로를 지정한다. (Runtime settings ➡️ Handler)  
          <img width="1518" alt="image" src="https://user-images.githubusercontent.com/21374902/219037044-bcfaa3c8-f956-4cc2-9b16-597201c208e4.png">  
      - Lambda 함수 테스트
        - Lambda Console에서 Test 탭으로 이동 후 원하는 이벤트 타입 (Input Paramter)를 설정 후 구동해본다.

  - ### (3) Typescript
    - #### (1) 원하는 경로에 SAM 프로젝트 설치  
      - `sam init`
      - `1 - AWS Quick Start Templates`
      - `1 - Hello World Example`
      - Use the most popular runtime and package type? (Python and zip) [y/N]: `N`
      - `14 - nodejs14.x`
      - `1 - Zip`
      - `2 - Hello World Example TypeScript`
      - Would you like to enable X-Ray tracing on the function(s) in your application?  [y/N]: `N`
      - Would you like to enable monitoring using CloudWatch Application Insights? [y/N]: `N`
      - Project name [sam-app]: `demo`
    
    - #### (2) Architectures 변경  
      - Mac을 사용하고 있다면 `template.yaml`을 열어서 Architectures를 `arm64`으로 변경
    
    - #### (3) 필요한 Module 및 Build  
      - `demo/hello-world$ yarn install`
      - `demo$ sam build`
    
    - #### (4) 함수 테스트  
      - `demo$ sam local invoke HelloWorldFunction --event events/event.json`



- ## 3. Lambda with DynamoDB 
  - ### (1) Java
    - dependencies in build.gradle
      ```yaml
      dependencies {
        implementation 'com.amazonaws:aws-lambda-java-core:1.2.1'
        implementation 'com.amazonaws:aws-lambda-java-events:3.11.0'
        implementation 'software.amazon.awssdk:dynamodb:2.19.31'
        implementation 'org.projectlombok:lombok:1.18.24'
        implementation 'com.google.code.gson:gson:2.9.0'
        annotationProcessor 'org.projectlombok:lombok:1.18.24'
      }
      ```
    - DDL
        - Create a table
            - Create a table with a simple primary key
            - Create a table with a composite primary key
        - List tables
        - Describe (get information about) a table
        - Modify (update) a table
        - Delete a table
    - DML
        - Retrieve (get) an item from a table
            ```java
          public class QueryTable implements RequestHandler<Object, QueryTableResponse> {
            private final Region REGION = Region.AP_NORTHEAST_2;
            private final String tableName = "table_name";
      
            @Override
            public QueryTableResponse handleRequest(Object input, Context context) {
              DynamoDbClient dynamoDbClient = DynamoDbClient.builder().region(REGION).build();
      
              HashMap<String,AttributeValue> keyToGet = new HashMap<String,AttributeValue>();
              keyToGet.put("name",AttributeValue.builder().s("beaver").build());
      
              GetItemRequest getItemRequest = GetItemRequest.builder()
                        .tableName(tableName)
                        .key(keyToGet)
                        .build();
      
              try{
                  Map<String,AttributeValue> returnedItem = dynamoDbClient.getItem(getItemRequest).item();
                  if(returnedItem != null) {
                      return QueryTableResponse.builder()
                                        .name(returnedItem.get("name").toString())
                                        .isReserved(true)
                                        .build();
                  }else {
                      return QueryTableResponse.builder()
                                        .isReserved(false)
                                        .build();
                  }
              }catch (Exception e){
                  return QueryTableResponse.builder()
                                      .isReserved(false)
                                      .build();
              }
            }
          }
          ```
        - Retrieve (get) an item from a table using the asynchronous client
        - Add a new item to a table
        - Update an existing item in a table
        - Delete an existing item in a table

  - ### (1) Typescript
    ```typescript
    // package.json
    ...
    "@types/aws-sdk": "^2.7.0",
    "aws-api-gateway-client": "^0.3.6",
    "aws-sdk": "^2.2.0",
    ...
    ```
    
    ```typescript
    // dynamoDbConfig.ts
    const dbClient = new DocumentClient{{ regison:  "ap-northeast-2" }};
    
    export enum DynamoDbMethod {
      DELETE = "DELETE",
      GET = "GET",
      PUT = "PUT",
      QUERY = "QUERY",
      SCAN = "SCAN",
      UPDATE = "UPDATE"
      ...  
    }
    
    export interface DynamoDbRequest {
        method: DynamoDbMethod;
        tableName: string;
        key?: { [key: string]: any };
        item?: { [key: string]: any };
        param?: any;
    }
    
    export const callDynamoDb = async (
        request: DynamoDbRequest
    ): Promise<any> => {
      let response;
      
      logger.info(`Request DynamoDb : [${request.method}] ${JSON.stringify(request)}`);
      
      switch (request.method){
        case DynamoDbMethod.DELETE:
          if(!request.key) throw new Error("Missing 'key' paramater");
          response = await dbClient.delete({
            TableName: request.tableName,
            Key: request.key,
            ...request
          }).promise();
        break;
        // ...
        case DynamoDbMethod.SCAN:
          response = await dbClient.delete({
            TableName: request.tableName,
            ...request
          }).promise();
          break;
        // ...
        
        logger.info(`Response DynamoDb : [${request.method}] ${JSON.stringify(response)}`);
            
        return response;
      }
    }
    ```
    
    ```typescript
    // dynamoDbService.ts
    const dynamoDbService = {
      scanSample: async (
        request: AmazonConnectRequest
      ): Promse<AmazonConnectResponse> => {
        try {
          const response = await callDynamoDb(request);
          return response.Count > 0 ? { ...response } : null;
        } catch(exceptoin) {
          logger.error("Excepton : " + exception);  
          throw new Error(exception);
        }   
      }
    }
    ```
    
    ```typescript
    // DynamoDbScanSample.ts
    interface AmazonConnectRequest {
      Name: string;
      Details: {
        ContactData: {
          Attributes: Record<string, string>;
          Channel: string;
          ContactId: string;
          CustomerEndpoint: {
            Address: string;
            Type: string;
          }
          ...
        }
      }
      Parameters?: Record<string, string> | null | undefined;
    }
    
    interface AmazonConnectResponse {
      ...
    }
    
    export const dynamoDbScanSampleHandler = async (request: AmazonConnectRequest): Promise<AmazonConnectResponse> => {
      const customerNumber = request.Details.Parameters.customerNumber;
      const dynamoDbRequest = {
        method: DynamoDbMethod.SCAN,
        tableName: "sample_table",
        param : {
          FilterExpression: "phoneNumber = :phoneNumber",
          ExpressionAttributeValues: {
            ":phoneNumber": customerNumber
          }
        }
      };
      return await sampleDb.scanSample(dynamoDbRequest);
    }
    ```

- ## 4. Reference
  - https://aws.amazon.com/ko/blogs/korea/new-accelerate-your-lambda-functions-with-lambda-snapstart/
  - https://varteq.com/java-vs-nodejs-on-aws-lambda-benchmark-survey/
  - https://docs.aws.amazon.com/sdk-for-java/latest/developer-guide/examples-dynamodb-items.html
  - https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GettingStarted.html
  - https://docs.aws.amazon.com/ko_kr/amazondynamodb/latest/developerguide/ScanJavaDocumentAPI.html

---
