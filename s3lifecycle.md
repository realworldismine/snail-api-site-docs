# S3 Lifecycle Management
## Planning
### Overview
- 현재 관리 중인 S3 Bucket에 대한 수명 주기 규칙을 적용한다.
- 자주 사용하지 않는 버킷에 대해서는 Storage Class를 변경함으로써 비용 절감을 도모하는 것이 좋을 것으로 예상됨
- 현재 사용 중인 Bucket은 다음과 같다.
  - codepipeline
  - frontend
  - images
  - temp images
- 각 Bucket에 대한 관리 필요 여부를 검토하여 수명 주기 규칙을 적용한다.

### Codepipeline Bucket
- Frontend 소스가 Commit되면 자동으로 Code Pipeline 작업을 수행함
- 그러나 Frontend는 자주 업데이트가 일어나지 않음
- 해당 Bucket에는 배포를 위한 기본적인 Package가 설치되어 있으며, Commit을 수행할 때에만 적용됨
- 수명 주기 규칙은 IA로 전환하는 것이 적합할 것, 지연 시간도 밀리초 단위이므로 실제 CodePipeline 작업에 지장이 없을 것으로 예상됨
- IA 유형은 One-Zone IA로 선택, 그 이유는 Commit할 때 사용될 패키지가 유실되면 안 되는 중요한 데이터가 아니며, 유실 시에도 다시 다운로드를 받을 수 있으므로 Standard IA를 사용하지 않아도 될 것
- 수명 주기 규칙을 사용하면 버저닝이 활성화되므로, 이전 버전에 대한 관리도 이루어져야 함, 그러나 이전 버전은 관리의 필요가 전혀 없으므로 삭제 규칙을 적용토록 함
- 이전 버전은 3일 이후에 삭제하도록 설정할 것
- Deleted Marker 설정 및 불완전한 Multipart Upload가 설정된 객체도 3일 이후에 삭제하도록 설정할 것

### Frontend Bucket
- Redoc 문서에 대한 정적 웹페이지 Bucket
- 특정 스토리지 용도가 아닌 정적 웹페이지에 해당되므로, 별도 정책은 수립하지 않음

### Image Bucket
- 실제 이미지가 저장된 Bucket
- 이미지는 버전 관리가 이미 되어 있는 상태
- 이에 따라 크게 이전 버전에 대한 수명주기 규칙과 현재 버전에 대한 수명주기 규칙을 설정토록 함

#### 이전 버전 수명주기 규칙
- 모두 최소 일자로 구성
- Standard-IA: 30일이 지나면 전환
- Glacier Flexible Retrieval: 90일이 지나면 전환
- Glacier Deep Archive: 180일이 지나면 전환
- Glacier Instant Retrival로 구성하지 않은 이유: 현재 버전도 아니고 이전 버전이 90일이 지났을 때 즉시 액세스가 필요한 상황이 생길 것으로 예상되지 않음

#### 현재 버전 수명주기 규칙
- Standard-IA: 30일이 지나면 전환
- 현재 버전의 이미지는 외부에서 API Gateway를 통해서 접근 가능한 데이터이므로 가용성이 보장되어야 하고, Standard-IA 이상의 수명주기 규칙을 적용할 이유는 없음

### Temporary Image Bucket
- 이미지 업로드에 대한 모든 처리가 완료되면 Bucket 내부의 데이터는 모두 자동으로 삭제되므로 별도의 수명주기 규칙을 적용하지 않아도 됨

## Implementation
### CodePipeline Bucket
- Go to management
- Create lifecycle rule
- Check `Apply to all objects in the bucket`
- Check `I acknowledge that this rule will apply to all objects in the bucket.`
- Check those
  - `Transition current versions of objects between storage classes`
  - `Permanently delete noncurrent versions of objects`
  - `Delete expired object delete markers or incomplete multipart uploads`
- Check `Transitions are charged per request`
- Transation current versions
  - One-Zone-IA: 30 days
- Permanently delete noncurrent versions: 3days
- Delete expired object delete markers or incomplete multipart uploads
  - Check all
  - Input 3 days
- Save

### Image Bucket
- Go to management
- Create lifecycle rule
- Check `Apply to all objects in the bucket`
- Check `I acknowledge that this rule will apply to all objects in the bucket.`
- Check those
  - `Transition current versions of objects between storage classes`
  - `Transition noncurrent versions of objects between storage classes`
- Transation current versions
  - Standard-IA: 30 days
- Transition noncurrent versions
  - Standard-IA: 30 days
  - Glacier Flexible Retrieval: 90 days
  - Glacier Deep Archive: 180 days