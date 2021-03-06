## 숙명여대와 함께하는 AWS Univ. Expo 핸즈온

😎 **Awskrug Univsersity Student Group (AUSG)** 핸즈온에 오신 것을 환영합니다.

핸즈온에서는 EC2를 사용하여 워드프레스를 실행시키고, RDS를 연동하여 직접 데이터를 저장하는 과정과 마지막으로 S3를 사용하여 정적 파일을 업로드하는 과정을 실습합니다.

실습에 이용되는 서비스는 프리티어 내에서 진행되며, 마지막 S3 부스에서 생성한 모든 서비스를 삭제하는 실습이 진행될 예정입니다. 불필요한 과금을 피하기 위해 S3 부스를 반드시 방문해주세요!

## AWS S3로 워드프레스 저장소 만들기

#### 🤗 이번 세션은 EC2, RDS 부스에서 만든 결과물에 저장소를 붙이는 세션입니다. 또한 세션 끝에 3가지 서비스를 모두 삭제하여 과금을 방지하는 세션이 있으니 EC2, RDS 부스를 모두 참여하신 뒤에 참여해주세요! 여의치 않다면 EC2 세션을 필수로 듣고 현재 세션을 들으신 후 RDS 세션을 들으신 후 자료 끝에 있는 서비스 삭제하기를 진행해주세요!

EC2 세션 - <https://github.com/AUSG/aws-univ-expo/tree/master/ec2

## 도입

### S3란 무엇인지에 대해서 먼저 알아 봅시다!

**AWS S3**(Simple Storage Service)는 Object Storage Service로 AWS에서 제공하는 파일 저장소입니다. 확장성, 데이터 가용성, 보안과 성능을 고루 갖춘 업계 최고의 스토리지 서비스입니다.

## 용어

아래의 용어는 가장 기초적인 수준에서의 개념만 설명하고 있습니다. 자세한 개념 설명은 [여기(공식홈페이지)](https://docs.aws.amazon.com/ko_kr/AmazonS3/latest/dev/Introduction.html#features)를 참고해주세요

공식홈페이지 - <https://docs.aws.amazon.com/ko_kr/AmazonS3/latest/dev/Introduction.html#features>

### 객체(Object)

S3는 모든 디렉토리 구조와 파일을 객체로 인식합니다. 객체는 파일의 메타데이터와 데이터를 가지고 있습니다. 객체는 키(이름) 및 버전 ID를 통해서 버킷 내에서 고유하게 식별됩니다.

### 버킷(Bucket)

버킷은 가장 높은 수준에서의 네임스페이스를 구성합니다. 버킷은 데이터를 저장하기 위한 Amazon S3의  기본 컨테이너입니다. 모든 객체는 어떤 버킷에 포함됩니다.

### 키(Key)

키는 버킷 내 객체의 고유한 식별자입니다. 버킷 내 모든 객체는 정확하게 하나의 키를 갖습니다. "버킷 + 키 + 버전"의 조합은 각 객체를 고유하게 식별합니다.

### 리전(Region)

리전(지역)은 Amazon S3에서 사용자가 만드는 버킷을 저장할 지역을 의미합니다. 지역에 따라 접속자의 지연 시간 최적화, 비용 최소화 등이 고려됩니다.

## 장점

### 1. 무한한 데이터 저장

각 객체는 최대 5TB까지 가질수 있으며, 버킷에는 객체를 무제한으로 저장할 수 있습니다.

### 2. 데이터 다운로드

데이터를 직접 다운로드 하거나 다른 사람이 그렇게 하도록(퍼블릭 권한) 합니다.

### 3. 권한

데이터를 Amazon S3 버킷으로 업로드 혹은 다운로드 하려는 사용자에게 액세스 권한을 부여하거나 해당 권한을 거부합니다. 3가지 유형의 사용자에게 업로드 및 다운로드 권한을 부여할 수 있습니다. 인증 메커니즘을 사용하면 데이터가 무단으로 액세스 되지 않도록 보호하는데 도움이 될 수 있습니다.

### 4. 표준 인터페이스

표준 기반 REST 및 SOAP 인터페이스를 사용합니다.

> HTTP를 통한 SOAP은 지원 중단 되었지만 HTTPS를 통해서 계속해서 사용할 수 있습니다.
>
> **하지만 REST 쓰세요. 두번 쓰세요 아니 세번 쓰세요**

## 요금

Amazon S3의 비용은 사용량만큼 지불합니다. 어떠한 추가요금이나 수수료가 없습니다.

### *프리 티어

AWS 프리 티어를 사용하는 고객은 Amazon S3를 무료로 시작할 수 있습니다. 

**신규 가입 고객**은 **1년동안 매달** 아래의 혜택을 받을 수 있습니다

- 5GB의 스토리지 용량
- 20,000건의 GET 요청
- 20,000건의 PUT, COPY, POST 또는 LIST 요청
- 15GB의 데이터 송신

[자세한 요금](https://aws.amazon.com/ko/s3/pricing/)은 사이트를 확인하세요

사이트 - <https://aws.amazon.com/ko/s3/pricing/>

## 사용하기

### 목표

이번 핸즈온의 목표는 워드프레스에서 S3를 파일 저장소로 사용하기 입니다.

### 뭐가 좋죠?

1. 용량으로부터 자유롭습니다.
2. S3의 고가용성, 99.999%의 내구성을 보장 받을 수 있습니다.
3. REST하게 관리되는 키를 통해 객체에 대한 접근이 자유롭습니다.
4. 미디어 컨텐츠에 대한 서버의 부하를 줄일 수 있습니다.
5. 미디어 파일들과 사이트를 분리함으로서 사이트 이전과 같은 작업을 더 수월히 할 수 있습니다.

## 더 해볼만 한 것

### - 정적 웹사이트 호스팅

#### 이제 S3를 Wordpress에 연결하는 실습을 해보겠습니다! [실습](./Exercise.md)



