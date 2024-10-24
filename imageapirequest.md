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
  - It is similar to `/images/{raw}/{file}` PUT API.
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

        # get an item using the device type and the image id by DynamoDB Table
        # get a host using event['headers'].get('Host', '')
        # get a stage using event.get('requestContext', {}).get('stage', '')
        # response an URL using the item's stored key, id, and uploaddate
    except Exception as e:
        # return 500 error code
```
- General configuration
  - Change the memory size: 1024MB
- Environment variables
  - `DYNAMODB_TABLE_NAME`: the table of DynamoDB(`image-metadata`)
- Modify a role: `GetSingleImage-role-xxxx`
  - Add policies: `AmazonDynamoDBBasicAccess`
- Make test scenarios
  - The previous Lambda function could not be tested using the "Test" feature because it handled uploaded images directly.
  - However, the current Lambda function accepts parameters or address values, making it possible to perform testing. Therefore, we want to create an event for testing purposes.
  - Test Scenarios
![image](https://github.com/user-attachments/assets/35c41449-3e09-4c12-a202-a2a57156269f)
  - Sample Code: `SinglImageEventTest`
```JSON
{
  "path": "http://abc.com/v0/snail/1",
  "headers": {
    "Host": "http://abc.com",
    "User-Agent": "Mozilla/5.0 (iPhone; CPU iPhone OS 14_0 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/14.0 Mobile/15A372 Safari/604.1"
  },
  "requestContext": {
    "stage": "v0"
  }
}
```

#### API Gateway
- Create Resource: `snail` - `{id}`
- Create Method(`Get`)
  - Integration type: Lambda function
  - Lambda function: `GetSingleImage`
  - Enable lambda proxy integration
- Edit Integration Request
  - Execution role: apply `apigateway-role`
- Deploy API
- Result
![image](https://github.com/user-attachments/assets/91f39c3b-d23f-400c-b8eb-a2c3535a8186)

- Using the result's url, go to the url
  - The image url is implemented previously, refer to the [page](apigateway.md#image-get-api)
  - Result
![image](https://github.com/user-attachments/assets/8e965078-c762-4241-90da-30270c200d1b)

### Get an image list address API
#### Lambda Function
- Name: `GetImageList`
- Code
```Python
# Combined a source code and a pseudo code
...

dynamodb = boto3.resource('dynamodb')

dynamodb_table_name = os.getenv('DYNAMODB_TABLE_NAME')

def lambda_handler(event, context):
    try:
        # extract url path
        # get parameters

        # extract user-agent info
        # parse user-agent info and get a device type

        # check page and if not page exists set a default value
        # check limit and if not page exists set a default value
        # check startdate
        # check enddate

        # get an item using the device type and the parameters by DynamoDB Table
        # get a host using event['headers'].get('Host', '')
        # get a stage using event.get('requestContext', {}).get('stage', '')

        # make a list from the results
        # response an URL using the item's stored key, id, and uploaddate
    except Exception as e:
        # return 500 error code
```
- General configuration
  - Change the memory size: 1024MB
- Environment variables
  - `DYNAMODB_TABLE_NAME`: the table of DynamoDB(`image-metadata`)
- Modify a role: `GetImageList-role-xxxx`
  - Add policies: `AmazonDynamoDBBasicAccess`
- Make test scenarios
  - Scenario List
![image](https://github.com/user-attachments/assets/9c622df1-cd79-44cd-af5f-ca9d76ce65fa)
  - Sample Code: `FullParamTest`
```JSON
{
  "path": "http://abc.com/v0/snail",
  "headers": {
    "Host": "http://abc.com",
    "User-Agent": "Mozilla/5.0 (iPhone; CPU iPhone OS 14_0 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/14.0 Mobile/15A372 Safari/604.1"
  },
  "requestContext": {
    "stage": "v0"
  },
  "queryStringParameters": {
    "startDate": "2024-10-18",
    "endDate": "2024-10-23",
    "page": "1",
    "limit": "10"
  }
}
```
#### API Gateway
- Create Resource: `snail`
- Create Method(`Get`)
  - Integration type: Lambda function
  - Lambda function: `GetImageList`
  - Enable lambda proxy integration
- Edit Integration Request
  - Execution role: apply `apigateway-role`
- Deploy API
- Result
![image](https://github.com/user-attachments/assets/541695a3-3963-41da-95aa-c87716d09b66)

### Get a random image list address API
#### Lambda Function
- Name: `GetRandomImageList`
- Code
```Python
# Combined a source code and a pseudo code
...

dynamodb = boto3.resource('dynamodb')

dynamodb_table_name = os.getenv('DYNAMODB_TABLE_NAME')

def lambda_handler(event, context):
    try:
        # extract url path
        # get parameters

        # extract user-agent info
        # parse user-agent info and get a device type

        # check count and if not page exists set a default value

        # get an item using the device type and the parameters by DynamoDB Table
        # get a host using event['headers'].get('Host', '')
        # get a stage using event.get('requestContext', {}).get('stage', '')

        # make a random list
        # response an URL using the item's stored key, id, and uploaddate
    except Exception as e:
        # return 500 error code
```
- General configuration
  - Change the memory size: 1024MB
- Environment variables
  - `DYNAMODB_TABLE_NAME`: the table of DynamoDB(`image-metadata`)
- Modify a role: `GetRandomImageList-role-xxxx`
  - Add policies: `AmazonDynamoDBBasicAccess`
- Make test scenarios
  - Scenario List

  - Sample Code: `ParamTest`
```JSON
{
  "path": "http://abc.com/v0/random",
  "headers": {
    "Host": "http://abc.com",
    "User-Agent": "Mozilla/5.0 (iPhone; CPU iPhone OS 14_0 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/14.0 Mobile/15A372 Safari/604.1"
  },
  "requestContext": {
    "stage": "v0"
  },
  "queryStringParameters": {
    "count": "1"
  }
}
```
#### API Gateway
- Create Resource: `random`
- Create Method(`Get`)
  - Integration type: Lambda function
  - Lambda function: `GetRandomImageList`
  - Enable lambda proxy integration
- Edit Integration Request
  - Execution role: apply `apigateway-role`
- Deploy API
- Result
