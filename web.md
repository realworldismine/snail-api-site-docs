# 정적 웹페이지 구현
## 초기 기획
- API Reference 페이지 추가: 모든 API 목록을 나타내도록 함
- 메인 화면 추가: 홈페이지 링크, 랜덤 이미지 조회 등 간단한 기능 추가 고려
- Reference: https://dog.ceo/

## 구현 방향
- 하나라도 제대로 하고 나서 기능 확장을 고려해 봅시다..
- 지금 구현할 서비스는 Serverless 기반으로 개발하는 것
- 너무 잘 하려고 할 필요는 없음, 그러므로 메인화면은 추가하지 않도록 함

## API Reference
- 이 것부터 만들도록 한다.
- 도구 선정: ReDoc으로 결정, 이유는 정적 HTML 파일 구현이 단순해서

## ReDoc 문서 생성
### 생성 예제
- 출처: https://redocly.com/docs/cli/installation

#### 설치
```
npm i -g @redocly/cli@latest
```

#### Open API 문서 획득
- 기존에 있는 문서 사용하거나
- 아니면 ReDoc에서 제공하는 예제 API Yaml 파일을 가져오거나

#### HTML 문서 생성
```
C:\Users\Temp> npx @redocly/cli build-docs museum.yaml  
Found undefined and using theme.openapi options
Prerendering docs

🎉 bundled successfully in: redoc-static.html (156 KiB) [⏱ 3ms].
```
 
#### 미리보기
![image](https://github.com/user-attachments/assets/2c5b5a88-6e3a-4166-be2a-c147baf0d5f7)

### TO-DO
- 위에서는 예제 OpenAPi 문서를 사용하였으므로, 이제 실제 API 문서를 만들어야 함(snail.yaml)
- Repository 생성 후, redoc/snail.yaml 파일을 넣어야 함
- Repo에서 S3로 실시간 동기화하는 방법을 검토해봐야 함
  - 실시간 동기화는 AWS CodePipeline을 사용하는 것이 가장 현실적인 방법이 될 것
  - WebHook으로 변경사항 Trigger한 다음, 실제 발생 시 S3에 업로드하는 절차로 수행
