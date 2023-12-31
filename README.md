# Argo Workflows Machine Learning Demo

This is an example machine learning workflow that will:
1. Receive a notification from a minio webhook event about an uploaded image to kick off the workflow
2. Download the image and crop all detected faces into multiple individual files
3. For each cropped face, parallelly run a face recognition model to identify the name of the face based on a directory of known faces
4. Suspend the workflow and wait for external approval from the UI
5. Succeed or fail the workflow depending on approval from the previous step

![Screenshot](docs/screenshot.png)

## Requirements
* Argo Workflows v3.5
* S3 bucket (see [minio](minio) directory for example in-cluster minio deployment)

## Workflow features showcased:
* WorkflowEventBinding for submitting a workflow from a minio webhook
* DAG template for defining dependent steps
* Script template for inlined Python code
* Input artifacts and artifact passing
* Data template for processing artifacts and generating fan-out steps
* Suspend template with approval
* Conditionals

## Details

### Workflow
* The actual [Workflow](face-detect/wf-face-detect.yaml)

### Webhook

A [WorkflowEventBinding](face-detect/minio-eventbinding.yaml) allows the Argo API server to act as a webhook receiver.

Minio is configured with a Webhook Event Destination to the Argo API server. e.g.:

* Event ID: argo-workflows-ml-demo
* Event destination: https://workflows.demo.akuity.io:443/api/v1/events/argo-workflows-ml-demo/
* Auth Token: `<bearer token from minio.service-account-token>`

![Minio Webhook Configuration](docs/webhook.png)

The `face-detect` bucket should be configured so that webhook event destination is subscribed to `PUT` events on the bucket:

![Minio Event Subscription](docs/event-subscription.png)

### S3 Bucket

The `face-detect` bucket should contain three top-level directories:
* `to-be-identified` - Any upload to this directory will kick off a face-detect workflow (filter is defined in the [WorkflowEventBinding](face-detect/minio-eventbinding.yaml)). For your convenience, you may use the [test-data](test-data) directory.
* `known-faces` - A pre-populated directory of images of known people. The name of the jpeg reflects the name of the known person (e.g. `Obama.jpg`). For your convenience, you may use the [known-faces](known-faces) directory.
* `wf-artifacts` - Intermediate storage used by the workflows. This can be periodically cleaned.

![Minio Event Subscription](docs/face-detect-bucket.png)
