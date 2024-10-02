# 구현 프로세스
## 정적 웹페이지 배포 프로세스
- Update Static Web Repo
- CodePipeline에서 WebHook을 사용하여 변경사항 감지
- OpenAPI Yaml 파일을 redocly CLI를 사용하여 자동으로 HTML 변환
- 변환된 HTML 파일을 S3 Bucket에 업로드

## 이미지 배포 프로세스
- Update Image Repo
- Make save request automatically
- Lambda에 의하여 Raw 데이터 이미지 저장 및 DynamoDB에 메타데이터 저장
- Lambda에 의하여 저장된 이미지를 해상도 별 Bucket에 추가로 저장 후 DynamoDB에 메타데이터 저장

## 정적 웹페이지 호출 프로세스
- Page Request
- API Gateway 요청 수행
- Return a static web page

## API 호출 프로세스
### Info Request API
- Info Request
- API Gateway를 통해서 요청 수행
- DynamoDB를 통한 쿼리 및 결과 Response

### Image Request API
- Image Request
- CloudFront에 캐싱된 이미지가 있을 경우 바로 전달, 없을 경우 API Gateway 요청 수행
- API Gateway에서 Client의 장비 및 메타데이터 획득
- DynamoDB를 통한 쿼리
- 각 Bucket의 정보에 대한 Image 정보 Reponse
