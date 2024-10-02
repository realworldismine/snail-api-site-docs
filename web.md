# Static Web Page Implementation
## Initial Planning
- Add API Reference Page: Display a list of all APIs.
- Add Main Page: Consider adding simple features such as a homepage link and random image retrieval.

## Implementation Direction
- Let's focus on doing one thing well before considering feature expansion.
- Development target is based on Serverless.
- Since system environment setup is a priority, the main page will be implemented later.

## API Reference
- Start by creating this.
- Tool Selection: Decided on ReDoc because generating a static HTML file is simple.

## ReDoc Documentation Installation and Generation Example
### Reference
- Source: https://redocly.com/docs/cli/installation

### Simple Example

#### Installation
```
npm i -g @redocly/cli@latest
```

#### HTML Document Generation
- Generation Command
```
npx @redocly/cli build-docs snail.yaml  
```
- Generation Result
```
Found undefined and using theme.openapi options
Prerendering docs

🎉 bundled successfully in: redoc-static.html (156 KiB) [⏱ 3ms].
```
 
#### Preview
![image](https://github.com/user-attachments/assets/2c5b5a88-6e3a-4166-be2a-c147baf0d5f7)

### Implement Direction
- The above example demonstrates how an OpenAPI document is generated and implemented using ReDoc.
- However, for actual implementation, the plan is to automatically convert the OpenAPI YAML file to HTML when uploaded to the GitHub repository.
- There are two main ways to automate the HTML conversion and upload:
  - Use GitHub Action to generate the HTML file with the redocly CLI, commit it to GitHub, and upload the HTML file to an S3 bucket.
  - Use AWS CodePipeline to generate the HTML file with the redocly CLI and upload it to an S3 bucket.
- Selected Approach
  - Decided to use the latter approach with AWS CodePipeline.
  - Using AWS CodePipeline allows triggering changes with the GitHub repository’s webhook and performing both the conversion and upload in one go.

## Deployment Using AWS CodePipeline and CodeBuild
### S3 bucket
- Purpose: Store a static web page
- Disable block public access
![image](https://github.com/user-attachments/assets/1bf6e7fb-5cb2-4d9f-868e-44ab26740c17)

- Others set default

### CodeBuild
- Purpose: To convert the OpenAPI YAML file into a Redoc document and generate a static HTML file as an artifact.
1. Source
  - Provider: Github
  - Crednetial: Default source credential
  - Repository: Select the API source repository
2. Primary source webhook events
  - Check rebuild every time a code change is pushed to this repository
  - Build type: Single build
3. Environment
  - Image: Managed
4. Buildspec
  - Use a buildspec file
  - Please add `buildspec.yml` in the repository below
```
version: 0.2

phases:
  install:
    commands:
      - npm install -g redoc-cli
  build:
    commands:
      - npx @redocly/cli build-docs snail.yaml -o redoc.html

artifacts:
  files:
    - redoc.html

cache:
  paths:
    - /root/.npm/**/*
```
  - It could be changed adding a static page

5. Architects - additional configuration
  - Cache type: Amazon S3
  - Cache bucket: a specific bucket for building a code (NOT API bucket)
  - Prefix: `build-cache`

6. S3 Permission (Important!!)
  - Why do we need to configure S3 permissions? 
    - To convert the OpenAPI YAML file into a Redoc document, you need to install npm and redocly-cli.
    - However, if you don't use caching, these packages will need to be installed every time the code is deployed.
    - To reduce this inconvenience, caching should be used, and S3 caching will be utilized.
    - However, if S3 permissions are not added, CodeBuild will not be able to access S3, so you must ensure that S3 permissions are added to the IAM role.
  - Usage
![image](https://github.com/user-attachments/assets/2323f70f-6697-47a4-89e4-16ebc3bea860)
    - You can see `Service role`, and click it.
    - Click Add permissions - Create inline policy.
    - Service: S3
    - Check `ListBucket`, `GetBucket`, `PutBucket`.
    - Check `Any` in `Resources`.

### CodePipeline
1. Choose pipeline settings
  - Queued
  - New service role
2. Add source stage
  - Github(Version 2)
  - Connection: use github account
  - Repository Name: select the API repository
  - Default Branch: `main`
3. Add build stage
  - AWS CodeBuild
  - Project Name: your CodeBuild project
4. Add deploy stage
  - Amazon S3
  - Input artifacts: BuildArtifact
  - Bucket: your API bucket
  - S3 Object Key: a specific bucket directory(e.g. `www`, `my-docs`)

### Deployment Result
![image](https://github.com/user-attachments/assets/3382b91e-4e72-48ad-9440-34af20ea6223)
