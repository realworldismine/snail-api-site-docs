# S3 Lifecycle Management
## Planning
### Overview
- Apply lifecycle policies to the currently managed S3 Buckets.
- For buckets that are not frequently used, it is expected that changing the storage class would help reduce costs.
- The currently used buckets are as follows:
  - codepipeline
  - frontend
  - images
  - temp images
- Review the need for managing each bucket and apply lifecycle policies accordingly.

### Codepipeline Bucket
- Automatically runs CodePipeline tasks when the frontend source is committed.
- However, frontend updates are not frequent.
- This bucket contains the basic packages needed for deployment, which are only used upon commit.
- It is suitable to apply a lifecycle rule to transition to IA (Infrequent Access) storage class. Since the latency is only in milliseconds, it is not expected to impact CodePipeline tasks.
- The IA type chosen is One-Zone IA because the packages used for commits are not critical data that must not be lost, and if they are lost, they can be downloaded again. Therefore, using Standard IA is unnecessary.
- When lifecycle rules are used, versioning is enabled, so previous versions must also be managed. However, there is no need to manage previous versions, so apply a deletion rule.
- Set previous versions to be deleted after 3 days.
- Set deleted markers and objects with incomplete multipart uploads to be deleted after 3 days.

### Frontend Bucket
- A static webpage bucket for ReDoc documents.
- Since it is a static webpage rather than specific storage usage, no additional policy is applied.

### Image Bucket
- A bucket that stores actual images.
- Images are already versioned.
- Therefore, set lifecycle rules for both previous and current versions.

#### Lifecycle Rule for Previous Versions
- All set to the minimum retention period:
  - Standard-IA: Transition after 30 days
  - Glacier Flexible Retrieval: Transition after 90 days
  - Glacier Deep Archive: Transition after 180 days
  - Glacier Instant Retrieval is not used because it is unlikely that previous versions older than 90 days will need instant access.

#### Lifecycle Rule for Current Versions
- Standard-IA: Transition after 30 days
- The current version of the images must be available externally via API Gateway, so availability must be guaranteed, and there is no need to apply a lifecycle rule beyond Standard-IA.

### Temporary Image Bucket
- Once all processing related to image uploads is complete, all data within the bucket is automatically deleted, so no lifecycle policy is needed.


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
![image](https://github.com/user-attachments/assets/1bd400e0-bacc-4bec-84e8-7357002e71ab)

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
- Save
![image](https://github.com/user-attachments/assets/a55cda78-7b14-427f-9bc7-cd5d5a78d6aa)
