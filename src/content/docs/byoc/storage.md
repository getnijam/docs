---
title: Your own storage
description: Store traces, screenshots, and videos in your own S3, Google Cloud Storage, or Azure bucket.
sidebar:
  order: 3
---

With bring-your-own-storage, test artifacts (traces, screenshots, and videos) are uploaded to **your**
bucket. Nijam stores only the object key alongside the run; the bytes are yours.

Nijam supports **AWS S3** (and any S3-compatible store, such as Cloudflare R2 or MinIO), **Google Cloud
Storage**, and **Azure Blob Storage**.

## How the browser reads artifacts

The dashboard and the Playwright trace viewer load artifacts **directly from your bucket** using
short-lived signed URLs (they never proxy through Nijam). That means your bucket must allow
cross-origin `GET` requests from:

- `https://www.nijam.dev` (inline screenshots and videos)
- `https://trace.playwright.dev` (the external trace viewer)

Configure CORS on your bucket before connecting, or traces and previews will fail to load in the
browser. Each provider's CORS snippet is below.

## AWS S3 (and S3-compatible)

**Credentials.** An access key ID and secret access key for an IAM user or role scoped to the bucket.

**Least-privilege IAM policy** (replace `YOUR_BUCKET`):

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["s3:PutObject", "s3:GetObject", "s3:DeleteObject"],
      "Resource": "arn:aws:s3:::YOUR_BUCKET/*"
    },
    {
      "Effect": "Allow",
      "Action": ["s3:ListBucket"],
      "Resource": "arn:aws:s3:::YOUR_BUCKET"
    }
  ]
}
```

**Bucket CORS:**

```json
[
  {
    "AllowedMethods": ["GET"],
    "AllowedOrigins": ["https://www.nijam.dev", "https://trace.playwright.dev"],
    "AllowedHeaders": ["*"],
    "MaxAgeSeconds": 3600
  }
]
```

**Connect.** In **Settings, Bring your own cloud, Storage**, choose **AWS S3 / S3-compatible** and fill
in the bucket, region, access key ID, and secret access key. For an S3-compatible store, also set the
**endpoint** (for example `https://<account>.r2.cloudflarestorage.com`); leave it blank for AWS S3. An
optional **key prefix** namespaces objects under a folder.

## Google Cloud Storage

**Credentials.** A service account with a JSON key. Paste the key's `client_email` and `private_key`.

**Least-privilege role.** Grant the service account **Storage Object Admin**
(`roles/storage.objectAdmin`) on the bucket (create, read, and delete objects), not on the whole
project:

```bash
gcloud storage buckets add-iam-policy-binding gs://YOUR_BUCKET \
  --member="serviceAccount:svc@project.iam.gserviceaccount.com" \
  --role="roles/storage.objectAdmin"
```

**Bucket CORS** (`cors.json`, then `gcloud storage buckets update gs://YOUR_BUCKET --cors-file=cors.json`):

```json
[
  {
    "origin": ["https://www.nijam.dev", "https://trace.playwright.dev"],
    "method": ["GET"],
    "responseHeader": ["*"],
    "maxAgeSeconds": 3600
  }
]
```

**Connect.** Choose **Google Cloud Storage** and fill in the bucket, service account email, and private
key (an optional project ID if it is not inferable from the key).

## Azure Blob Storage

**Credentials.** The storage account name and one of its access keys.

**Least-privilege.** Use a storage account (or a container) dedicated to Nijam. Access keys grant full
control of the account, so isolate it; scoped SAS support may come later.

**Container CORS.** In the storage account, under **Resource sharing (CORS), Blob service**, add a rule:
allowed origins `https://www.nijam.dev` and `https://trace.playwright.dev`, allowed method `GET`,
allowed and exposed headers `*`, max age `3600`.

**Connect.** Choose **Azure Blob Storage** and fill in the **container** name (the "bucket" field), the
account name, and the account key.

## Rotating and disabling

- **Rotate**: re-enter new credentials and click **Connect**.
- **Disable**: click **Disable storage**. New artifacts go to Nijam's managed storage; the objects
  already in your bucket stay there and stop appearing in Nijam until you reconnect.
