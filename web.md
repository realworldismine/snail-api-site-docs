# 정적 웹페이지 구현
## 초기 기획
- API Reference 페이지 추가: 모든 API 목록을 나타내도록 함
- 메인 화면 추가: 홈페이지 링크, 랜덤 이미지 조회 등 간단한 기능 추가 고려

## 구현 방향
- 하나라도 제대로 하고 나서 기능 확장을 고려해 봅시다.
- Serverless 기반 개발 목표
- 시스템 환경 구축이 우선이므로, 메인화면은 추후 구현토록 함

## API Reference
- 이 것부터 만들도록 한다.
- 도구 선정: ReDoc으로 결정, 이유는 정적 HTML 파일 구현이 단순해서

## ReDoc 문서 설치 및 생성 예제
### Reference
- 출처: https://redocly.com/docs/cli/installation

### Simple Example

#### Installation
```
npm i -g @redocly/cli@latest
```

#### HTML 문서 생성
- 생성 구문
```
npx @redocly/cli build-docs snail.yaml  
```
- 생성 결과
```
Found undefined and using theme.openapi options
Prerendering docs

🎉 bundled successfully in: redoc-static.html (156 KiB) [⏱ 3ms].
```
 
#### 미리보기
![image](https://github.com/user-attachments/assets/2c5b5a88-6e3a-4166-be2a-c147baf0d5f7)

### 구현 방향
- 위 예제는 OpenAPI 문서를 redoc으로 어떻게 파일이 생성되고 구현되는 지를 시연한 예제임
- 그러나, 실제 구현 시에는 Github Repository에 OpenAPI Yaml 문서를 올리면 자동으로 html로 변환하도록 구현할 예정
- 자동으로 html 변환 및 등록하는 방법은 크게 2가지가 있음
  - Github Action을 사용하여 redocly CLI를 사용하여 html 문서 생성 및 Github에 Commit 후, s3 bucket에 html 파일 업로드
  - AWS CodePipeline을 사용하여 redocly CLI 사용 및 html 문서 생성 후, s3 bucket에 html 파일 업로드
- 선정 결과
  - 후자인 AWS CodePipeline을 사용하는 방법으로 결정
  - AWS CodePipeline을 사용하면 Github Repository를 WebHook을 사용하여, 변경사항을 Trigger한 다음 변환 및 업로드를 한 번에 수행할 수 있음

## AWS CodePipeline을 사용한 배포
- TBD
