# 달팽이 API 구축

## 구축 목표
- AWS Serverless를 기반으로 해서 간단한 달팽이 사이트와 API를 구축한다.

## 구축 기능
- 달팽이 API Static HTML Page
  - ReDoc API Reference
  - Random Image Usage (TBD)
  - Snail Info (TBD)
- API Reference
  - Snail Info API
  - Snail Image API
  - Snail Random Image API

## Services
- CodePipeline
- CloudFront
- API Gateway
- Lambda
- DynamoDB
- S3

## Repositories
- Static Web Page Repository
  - Uses AWS CodePipeline
  - OpenAPI yaml 파일 변경 시, redocly CLI를 사용하여 Static HTML 파일 생성 후 S3 Bucket에 업로드
- Image Repository
  - Uses Github Action
  - Repository 변경 시, github action을 사용하여 S3 Bucket에 업로드

## S3 Buckets
- Front-end Static HTML Bucket
- Image Raw Bucket
- Image 해상도 별 Bucket
  - 모바일(Small)
  - 태블릿(Middle)
  - PC(Big)
 
### Sub Pages
- [Static Web Page Implementation](web.md)
- [Processes](process.md)
