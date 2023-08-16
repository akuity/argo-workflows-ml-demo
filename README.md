# Argo Workflows Machine Learning Demo

This is an example machine learning workflow which will:
1. Receive a notification from a minio webhook event about an uploaded image
2. Downloads the image, and crop the faces into multiple files
3. For each cropped face, run a face recognition model to identify the name of the face based on a s3 bucket of known faces

Workflow features showcased:
* WorkflowEventBinding for submitting a workflow from a minio webhook
* DAG template for defining dependant steps
* Script template for inlined python code
* Input artifacts and artifact passing
* Data template for processing artifacts and generating fan-out steps
* Suspend template with approvals
* Conditionals