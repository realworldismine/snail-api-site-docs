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

## Implementation

### IAM Policy
- Define policies for S3, SQS, and Lambda functions.
- `ImageMetadataDynamoDBBasicPolicy`: Policy for performing Put, Delete, and Get actions on DynamoDB.
- `PollPutRawImageQueuePolicy`: Policy for the `RawImagePutProcess` Lambda function to retrieve messages from the `PutRawImage` queue.
- `SendPutResolutionBasedImageQueuePolicy`: Policy for the `RawImagePutProcess` Lambda function to send messages to the `PutResolutionBasedImageQueue`.
- `PollPutResolutionBasedImageQueuePolicy`: Policy for the `ResolutionBasedImagePutProcess` Lambda function to retrieve messages from the `PutResolutionBasedImageQueue`.
- `SnailImagesBucketGetPolicy`: Policy to retrieve images from the S3 bucket.
- `SnailImagesBucketPutPolicy`: Policy to store and delete images in the S3 bucket.
- `SnailTempImagesGetAndDeletePolicy`: Policy to retrieve and delete images in the temporary S3 bucket.

### Lambda Function
- Split the functionality of the existing Lambda function used for image put processing into two separate functions: one for processing raw images and another for processing resolution-adjusted images.

#### Processing Raw Images
- Name: `RawImagePutProcess`
- Additional Policies: ImageMetadataDynamoDBBasicPolicy, PollPutRawImageQueuePolicy, SendPutResolutionBasedImageQueuePolicy, SnailImagesBucketPutPolicy, SnailTempImagesGetAndDeletePolicy
- Code
```Python
def lambda_handler(event, context):
    try:
        # Send metadata added to DynamoDB as a message to the SQS queue for image resolution processing
        # Delete images stored in the S3 Temporary Bucket

        # check an extension whether a file is an image or not
        # make a hash value
        # make a hashed file name

        # check current original file
        # if exists uses current id, otherwise then uses new id
        # put a raw file to S3 object with the hashed file name

        # declare the dynamodb table
        # make a image id using imagetype/originalfile/hashedfile
        # insert an item with the raw file's metadata

        # Process data received from SQS
        # Retrieve image information stored in the S3 Temporary Bucket based on the data received from SQS

        # return 200 succcess code

    except UnidentifiedImageError:
        # return 400 invalid image error code
    except Exception as e:
        # return 500 error code
```

#### Processing Resolution-Adjusted Images
- Name: `ResolutionBasedImagePutProcess`
- Additional Policies: ImageMetadataDynamoDBBasicPolicy, PollPutResolutionBasedImageQueuePolicy, SnailImagesBucketGetPolicy, SnailImagesBucketPutPolicy
- Code:
```Python
def lambda_handler(event, context):
    try:
        # Process data received from SQS
        # Retrieve original image information from the S3 bucket based on the data received from SQS

        # open an image
        # declare image resolutions

        for image_type, size in resolutions.items():
            # make a resized image
            # save a resized image using BytesIO buffer
            # put a resized file to S3 object with the hashed file name
            # insert an item with the resized file's metadata

        # return 200 succcess code

    except UnidentifiedImageError:
        # return 400 invalid image error code
    except Exception as e:
        # return 500 error code
```

### SQS
- Set as Standard Queue
- PutRawImageQueue: Queue for processing raw images
- Uses RawImagePutProcess as a Lambda trigger
- PutResolutionBasedImageQueue: Queue for processing resolution-adjusted images
- Uses ResolutionBasedImagePutProcess as a Lambda trigger

### S3 Bucket
- Purpose: Store a temporary image
- Event Notification
  - Prefix: `images/`
  - Event types: Check `All object create events`
  - Destination: SQS Queue
  - SQS Queue: `PutRawImageQueue` ARN

### API Gateway
- Modify `raw/{object}` PUT API
  - Select Integration Request
  - AWS Service: S3
  - Action Type: Use path override
  - Path override: `snail-temp-images-dev/images/{object}`
