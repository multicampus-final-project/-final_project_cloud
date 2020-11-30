# 참조

https://medium.com/@JCDubs/api-gateway-s3-proxy-a72e398b4d03



# API 게이트웨이 S3 프록시 

### S3 버킷 생성부터 파일올리기, api 게이트웨이 생성과정은 생략함 

### 

### IAM Role 생성

* 역할 만들기

* 사용 사례 선택에서 S3 선택

* AmazonS3ReadOnlyAccess 정책 선택

* 역할 이름 api-read-s3으로 생성 완료

* 생성 후 신뢰관계 탭에서 신뢰 관계 편집

  ```
  {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Effect": "Allow",
        "Principal": {
          "Service": [
            "lambda.amazonaws.com",
            "apigateway.amazonaws.com"
          ]
        },
        "Action": "sts:AssumeRole"
      }
    ]
  }
  ```

  

### API 게이트웨이 리소스 생성

* 리소스 이름: item

  리소스 경로: {item}

  

### API 게이트웨이 GET 메서드 생성 및 설정

* 통합 유형: AWS 서비스

  AWS 리전: us-east-1

  AWS Service: S3

  HTTP 메서드: GET

  작업 유형: 경로 재정의 사용

  경로 재정의(선택사항): *버킷이름*/{item}

  실행 역할: 앞서 만든Role의 ARN 복붙

* GET > 통합요청 > URL 경로 파라미터 추가

  | Name | Mapped from              |
  | ---- | ------------------------ |
  | item | method.request.path.item |



### API 배포(CORS 활성화가 필요한지는 모르겠지만 했음)

* 배포하고 [배포 url]/index.html 로 접속해보면 html태그들이 보인다!

  -> JSON 형식으로 응답이 받아졌기 때문

  

### 응답을 HTML로 받기

1. GET > 메서드 요청 > HTTP 요청 헤더

   * Content-Type, Content-Disposition 추가

   

2. GET > 통합 요청 > HTTP 헤더

   | 이름                | 다음에서 매핑됨                           |
   | ------------------- | ----------------------------------------- |
   | Content-Type        | method.request.header.Content-Type        |
   | Content-Disposition | method.request.header.Content-Disposition |

3. GET > 메서드 응답 > HTTP 상태 200 > 200에 대한 응답 헤더

   * Content-Type, Content-Disposition 추가

   

4. GET > 통합 응답 > HTTP 상태

   * HTTP 상태 regex 값 200으로 변경 후 저장
   * 헤더 매핑 탭에 추가

   | 응답 헤더           | 매핑 값                                             |
   | ------------------- | --------------------------------------------------- |
   | Content-Type        | integration.**response**.header.Content-Type        |
   | Content-Disposition | integration.**response**.header.Content-Disposition |

   

   

