# GCloud

## CLI

- Login: `gcloud auth login`
- List accounts: `gcloud auth list`
- Select account: `gcloud config set account 'paul.coghlan@gmail.com'`
- List service accounts: `gcloud --project dev-storage iam service-accounts list --limit 20`
- List service accounts from logs: `gcloud --project dev-storage logging read 'timestamp>="2019-01-24T00:00:00Z" AND resource.type="service_account" AND protoPayload.methodName="google.iam.admin.v1.CreateServiceAccount"'`
- Set project: `gcloud config set project my-project`
- Set zone: `gcloud config set compute/zone`
