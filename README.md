# gcp-secure-cloudrun-api-terraform
🛠 Deployment Debugging & Resolution
During deployment of the sentinal-api service, a 404 Page not found error was encountered when accessing the Cloud Run URL.
Root Cause
The issue was caused by deploying the service to one GCP project while testing the URL from another project context.
Specifically:
gcp-secure-cloudrun-api (original project)
gcp-secure-cloudrun-api-tf (Terraform-managed project)
The CLI project context was misaligned, causing the service to appear missing and return 404 errors.
