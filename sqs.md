# Asynchronized Processing - SQS Implementation
## Initial Planning
### Key Changes
- Created S3 Bucket for temporary image storage
- Split existing image processing Lambda function into two: original processing and resolution-based processing
- Created SQS for managing metadata of original images during storage
- Created SQS for managing metadata of resolution-based images during storage
- Created SQS for managing metadata of resolution-based images during deletion

### Image Upload Process
#### API Gateway Implementation
- API Gateway is directly connected to the Lambda function
- However, the target should be changed to send to the S3 Bucket instead
- The original image must be saved with its original name, so the original image information must also be included in the parameters and passed along

#### Temporary S3 Bucket Implementation
- Created a temporary bucket for processing with Lambda through SQS
- Images must be stored with their original names
- Upon a PUT request to the API Gateway, images are stored in S3
- When stored in S3, an S3 event is generated to send metadata to SQS
- Once processing is complete in Lambda, the corresponding image should be deleted

#### SQS Implementation for Updating a Raw Image
- Stores metadata about the S3 object
- SQS must generate a trigger to send it to the original image processing Lambda function

#### Lambda Implementation for Updating a Raw Image
- When metadata is received, the image must be retrieved from the temporary S3
- Stores the original image in the S3 bucket and inserts metadata into DynamoDB
- Sends metadata to the SQS queue for resolution-based image processing
- Once all processing is complete, the image in the temporary S3 bucket must be deleted

#### SQS Implementation for Updating Resolution-based Images
- Stores metadata received from the Lambda function
- SQS must generate a trigger to send it to the resolution-based image processing Lambda function
- Lambda Implementation for Updating Resolution-based Images
- When metadata is received, stores the resolution-based image in the S3 bucket and inserts it into DynamoDB

### Image Delete Process
#### API Gateway Implementation
- API Gateway is directly connected to the Lambda function, no additional modifications required

#### Lambda Implementation for Deleting a Raw Image
- Deletes the original image from the S3 bucket and changes the status of metadata in DynamoDB
- Sends metadata to the SQS queue for deleting resolution-based images
- Once all processing is complete, adds a feature to delete the image from the temporary S3 bucket

#### SQS Implementation for Deleting Resolution-based Images
- Stores metadata received from the Lambda function
- SQS must generate a trigger to send it to the resolution-based image deletion Lambda function

#### Lambda Implementation for Deleting Resolution-based Images
- When metadata is received, deletes the resolution-based image from the S3 bucket and updates the status in DynamoDB