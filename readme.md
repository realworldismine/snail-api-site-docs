# 달팽이 API 구축

## Reference
- https://dog.ceo

## 구축 목표
- AWS Serverless를 기반으로 해서 간단한 달팽이 사이트와 API를 구축한다.

## 구축 기능
- 달팽이 API Frontend Page:
  - API Usage
  - Random Image Usage
- API Reference
  - 달팽이 종 API
  - 달팽이 랜덤 이미지 API
  - 달팽이 종별 이미지 리스트 API

## 구축 서비스
- CloudFront
- API Gateway
- Lambda
- DynamoDB
- S3

## 이미지 Repo
- 별도 생성
- Master Branch에 변경사항 발생 시 이미지 Repo 갱신
- GitOps를 활용하여 자동으로 땡겨간 후, S3와 DynamoDB에 이미지 및 메타데이터 저장

## S3 Buckets
- Front-end 정적 Buckets - ReDoc 사용
- Image Raw Bucket
- Image 해상도 별 Bucket
  - 모바일(Small)
  - 태블릿(Middle)
  - PC(Big)
 
## Get Image Process
- Image Request
- CloudFront에 캐싱된 이미지가 있을 경우 바로 전달, 없을 경우 API Gateway 요청 수행
- Lambda에서 Client의 장비 및 메타데이터 획득
- DynamoDB를 통한 쿼리
- 각 Bucket의 정보에 대한 Image 정보 Reponse

## Save Image Process
- Update Image Repo
- Make save request automatically
- Lambda에 의하여 Raw 데이터 이미지 저장 및 DynamoDB에 메타데이터 저장
- Lambda에 의하여 저장된 이미지를 해상도 별 Bucket에 추가로 저장 후 DynamoDB에 메타데이터 저장

## 기타
- 다 만들면 이 Repo Public으로 바꾸고 문서는 전부 다 영문화 진행 예정
