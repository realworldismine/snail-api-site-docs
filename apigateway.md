# API Gateway Implementation
## Initial Planning
### Process
- To update the Image Repository, setting up an API Gateway must precede.
- The reasons are as follows:
  - The Static Web Repository triggers AWS CodePipeline via WebHook when a Push event occurs.
  - On the other hand, when a Push event occurs in the Image Repository, GitHub Action uploads the files to the S3 Bucket.
  - However, with the use of API Gateway, all data in the S3 Bucket must be passed through the API Gateway.
  - Instead of directly uploading to the S3 Bucket, GitHub Action should send POST, PUT, and DELETE requests to the API Gateway.
  - Therefore, API Gateway and the Image S3 Bucket must be created first.
- The API will include POST requests for images, as outlined in the initial OpenAPI document, and will also add PUT and DELETE requests.
- The same S3 Bucket used for API operations can be used for the Image Bucket, which will be determined during the implementation process.

### API Gateway

### S3 Bucket