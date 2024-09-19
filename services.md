# 서비스 구현 절차

1. 정적 웹 페이지 호스팅을 위한 S3 Bucket을 생성한다.
2. 이미지 저장소를 위한 S3 Bucket을 생성한다.
3. DynamoDB를 생성한다.
4. API Gateway를 생성한다.
5. 웹페이지 Repository를 생성한다.
6. 이미지 Repository를 생성한다.

# 정적 웹페이지 및 Repository 생성 방안
- 목표는, 코딩을 단 한 줄도 하지 않고 웹호스팅을 하는 것이다.
- 웹페이지 Repo는 Github Action 등을 사용해서 정적 웹호스팅 Bucket에 연동시키도록 한다.
- Redoc을 사용하는 것이 좋다고 하여 고려해 보도록 한다.

# 이미지 Bucket 및 Repository 생성 방안
- 이미지 Repo는 CodePipeline을 연동해서 Push 시, DynamoDB 입력 및 이미지 Bucket에 연동시키도록 한다.
- 변경된 내용 확인하고, 해당 파일정보만 가져온후, AWS Lambda에서 DynamoDB 입력 및 Bucket 추가 및 변경만 하면 되는 것 아니겠는가.
- 추가, 변경, 삭제 시 S3 동작을 어떻게 할 것인지 고려는 해야 할 수 있음

