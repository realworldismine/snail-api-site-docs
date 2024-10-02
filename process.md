# Implementation Process
## Static Web Page Deployment Process
- Update Static Web Repository
- Detect changes using WebHook in CodePipeline
- CodeBuild, integrated with CodePipeline, automatically converts OpenAPI YAML file to HTML using redocly CLI
- Upload the converted HTML file to the S3 Bucket

## Image Deployment Process
- Update Image Repository
- Make save request automatically
- Lambda stores raw image data and saves metadata in DynamoDB
- Lambda additionally stores the image in different resolution-specific Buckets and updates the metadata in DynamoDB

## Static Web Page Request Process
- Page Request
- Perform API Gateway request
- Return a static web page

## API Request Process
### Info Request API
- Info Request
- Perform request through API Gateway
- Query DynamoDB and return the result as a response

### Image Request API
- Image Request
- If the image is cached in CloudFront, return it immediately; otherwise, perform a request via API Gateway
- API Gateway retrieves client device information and metadata
- Query DynamoDB
- Return image information from each Bucket
