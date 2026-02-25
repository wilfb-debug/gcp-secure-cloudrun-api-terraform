# GCP Secure Cloud Run API (Terraform)

Secure Flask API deployed to Google Cloud Run.
Built with Cloud Build + Artifact Registry.
Infrastructure managed with Terraform.

---

## Project Info

Service name: sentinal-api  
Region: europe-west2  
Terraform project: gcp-secure-cloudrun-api-tf  
Runtime: Cloud Run  
Framework: Flask + Gunicorn  

Endpoints:
- / → {"status":"Sentinal API running"}
- /health → {"status":"ok"}

---

## Architecture Overview

- Cloud Run (API runtime)
- Artifact Registry (container storage)
- Cloud Build (image build pipeline)
- Terraform (infrastructure provisioning)
- IAM (cross-project access control)

---

## Security Design

- Cloud Run ingress set to internal-only
- Service account explicitly defined
- Cross-project Artifact Registry access controlled via IAM binding
- No public unauthenticated access

---

## What This Project Demonstrates

- Real-world cloud debugging
- Cross-project IAM troubleshooting
- Terraform state management
- Cloud Run production deployment
- Understanding of GCP service agents

---

# Debugging Incident 1 — 404 Error

## Problem
Cloud Run URL returned HTTP 404.

## Root Cause
Wrong gcloud project selected.

Service deployed in:
gcp-secure-cloudrun-api-tf

CLI pointed to:
gcp-secure-cloudrun-api

## Fix
gcloud config set project gcp-secure-cloudrun-api-tf
gcloud auth application-default set-quota-project gcp-secure-cloudrun-api-tf

Result:
curl returned HTTP 200.

---

# Debugging Incident 2 — Artifact Registry Permission Denied

## Problem
terraform apply failed with:
artifactregistry.repositories.downloadArtifacts denied

## Root Cause
Cloud Run runtime service agent lacked read permission on Artifact Registry repository in image project.

## Fix

DEPLOY_PROJECT="gcp-secure-cloudrun-api"
PROJECT_NUM=$(gcloud projects describe "$DEPLOY_PROJECT" --format="value(projectNumber)")
SA="service-${PROJECT_NUM}@serverless-robot-prod.iam.gserviceaccount.com"

gcloud artifacts repositories add-iam-policy-binding cloud-run-source-deploy \
  --project gcp-secure-cloudrun-api-tf \
  --location europe-west2 \
  --member "serviceAccount:${SA}" \
  --role "roles/artifactregistry.reader"

Re-run:
terraform apply

Result:
Apply complete! Resources: 0 added, 0 changed, 0 destroyed.

---

## Screenshots

---

## Screenshots

### 404 Before Fix
![404 Before Fix](docs/screenshots/01-404-error-before-fix.png)

### Wrong Project Context
![Wrong Project](docs/screenshots/02-wrong-gcloud-project-context.png)

### No Services In TF Project
![No Services](docs/screenshots/03-no-services-in-tf-project.png)

### Successful Deploy
![Successful Deploy](docs/screenshots/05-successful-cloudrun-deploy.png)

### HTTP 200 Success
![HTTP 200](docs/screenshots/06-root-endpoint-200.png)

### Artifact Registry IAM Binding Success
![IAM Binding](docs/screenshots/17-artifact-registry-iam-binding-success.png)

### Zero Drift Proof
![Zero Drift](docs/screenshots/19-terraform-pal-zero-drift.png)

All screenshots located in:

docs/screenshots/

