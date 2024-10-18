# Image API Request Implementation
## Initial Planning
### How to implement API Gateway and Lambda
- Implement `/snail/{imageID}` GET API
  - Using a lambda function
  - In a lambda function, it check the application header and determine a resolution, and responds an image's address.
  - Refer to the redoc document
    - The value of imageID is a numeric value, such as 1, 2, 3, 4, etc.
- Implement `/snail` GET API
  - Using a lambda function
  - In a lambda function, it check the application header and determine a resolution, and query the data using parameters.
  - Refer to the redoc document
    - it uses some parameters, such as `StartDate`, `EndDate`, `Page`, `Limit`.
    - it needs how to implement query using this parameters.
- Implement `/snail` POST API
  - It is similar to `/image/{raw}/{file}` PUT API.
  - However, the redoc document is needed to change because its request body has only `url` but would be added to `data`.
  - If using this API, it reviews API request body, the execute image processing.
  - It could be used step function for lambda functions.
- Implement `/random` GET API
  - It is similar to `/snail` GET API.
  - Using a random function, make a list and utilize a page and a limit.
  - Refer to the redoc document
    - it uses some parameters, such as `Page`, `Limit`.
    - it needs how to implement query using this parameters.

## Implementation
### Get a single image address API
#### Lambda Function
- Name: `GetSingleImage`
- Code
```Python
# Combined a source code and a pseudo code
...

dynamodb = boto3.resource('dynamodb')

dynamodb_table_name = os.getenv('DYNAMODB_TABLE_NAME')

def lambda_handler(event, context):
    try:
        # extract url path
        # get an image id
        # if failed, return 404 error code

        # extract user-agent info
        # parse user-agent info and get a device type

        # get an item using the device type and the image id
        # response an URL using the item's stored key
    except Exception as e:
        # return 500 error code
```

#### API Gateway
- Create Resource: `snail` - `{id}`
- Create Method(`Get`)
  - Integration type: Lambda function
  - Lambda function: `GetSingleImage`
  - Enable lambda proxy integration
- Edit Integration Request
  - Add Path Parameter
    - `object`: `method.request.path.object`
    - `resolution`: `method.request.path.resolution`
- Edit Method Response
  - Execution role: apply `apigateway-role`
- Deploy API

