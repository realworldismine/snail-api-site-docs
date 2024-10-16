# Image API Request Implementation
## Initial Planning
### How to implement API Gateway and Lambda
- Implement `/image/{imageID}` GET API
  - Using a lambda function
  - In a lambda function, it check the application header and determine a resolution, and responds an image's address.
  - Refer to the redoc document
    - it specifies `SnailID`, but it uses a full path of the image repository, such as `/Snail1/ABCD.png`
    - it is needed to find brining the `UserAgent` information.
- Implement `/image` GET API
  - Using a lambda function
  - In a lambda function, it check the application header and determine a resolution, and query the data using parameters.
  - Refer to the redoc document
    - it uses some parameters, such as `StartDate`, `EndDate`, `Page`, `Limit`.
    - it needs how to implement query using this parameters.
    - Also, the redoc document is needed to change because `UserAgent` is not exist.
- Implement `/image` POST API
  - It is similar to `/image/{raw}/{file}` PUT API.
  - However, the redoc document is needed to change because its request body has only `url` but would be added to `data`.
  - If using this API, it reviews API request body, the execute image processing.
  - It could be used step function for lambda functions.
- Implement `/random` GET API
  - It is similar to `/image` GET API.
  - Using a random function, make a list and utilize a page and a limit.
  - Refer to the redoc document
    - it uses some parameters, such as `UserAgent`, `Page`, `Limit`.
    - it needs how to implement query using this parameters.

## Implementation