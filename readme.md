# Snail API Implementation

## 구축 목표
- Implement a simple Snail web page and API based on AWS Serverless.

## 구축 기능
- Snail API Static HTML Page
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
  - Uses AWS CodePipeline & CodeBuild
  - When the OpenAPI YAML file is changed, generate a static HTML file using the redocly CLI and upload it to the S3 bucket.
- Image Repository
  - Uses Github Action
  - When the repository is updated, upload to the S3 bucket using GitHub Actions.

## S3 Buckets
- Front-end Static HTML Bucket
- Bucket for raw image
- Bucket for each image resolution
  - Mobild(Small)
  - Tablet(Middle)
  - PC(Big)
 
### Sub Pages
- [Static Web Page Implementation](web.md)
- [Processes](process.md)
