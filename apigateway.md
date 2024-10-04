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

## IAM Role - API Gateway to S3
### Overview
- To connect an S3 bucket object with API Gateway, an IAM Role must be configured and linked.
- The configured IAM Role can be applied to the Execution Role when creating the Method in API Gateway.

### Create a role
- Trusted Entity Type: `AWS Service`
- Use Case: `API Gateway`
- Others are Default

### Role Permission
- Add Permissions - Attach Policies
- Check `AmazonS3FullAccess`

## Create an API Gateway
### Create API
- Select and build `Rest API`
- API endpoint type
  - Select `Edge-Optimized`
  - Consider using CloudFront as well.

### Redoc API
- Create Resource: `redoc`
- Create Method(`GET`)
  - Integration Type: AWS Service
  - AWS Service: S3
  - Action Type: Use path override
  - Path override: `{s3 bucket}/{path}`
  - Execution role: above the IAM role
- Modify Method Response
  - Add Header: `Content-Type`
- Modify Integration Response
  - `Content-Type`'s mapping value: `integration.response.header.Content-Type`
- The Method & Integration Response must be set as described above to display static HTML content instead of a JSON response.
- Deploy API: Select New Stage and enter a stage name for each version.

## Change S3 permissions
- Check block all public access
- By configuring it this way, the S3 bucket will not be directly accessible from the outside, and access will only be possible through API Gateway.
