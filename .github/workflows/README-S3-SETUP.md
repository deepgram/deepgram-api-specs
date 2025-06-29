# S3 Storage Setup for Vector Store Metadata

This workflow uses S3-compatible storage (Tigris) to persist the vector store metadata between workflow runs.

## Implementation

The workflow uses the [`urbann3/s3go-action@v1`](https://github.com/marketplace/actions/s3go-action) marketplace action for fast S3 operations. This action uses Go concurrency for improved performance when uploading/downloading files.

## Required GitHub Secrets

You need to configure the following secrets in your GitHub repository settings:

1. **TIGRIS_ACCESS_KEY_ID**: Your Tigris access key ID
2. **TIGRIS_SECRET_ACCESS_KEY**: Your Tigris secret access key
3. **TIGRIS_ENDPOINT_URL**: The S3-compatible endpoint URL (e.g., `https://fly.storage.tigris.dev`)
4. **TIGRIS_REGION**: The AWS region (e.g., `auto`)
5. **TIGRIS_BUCKET_NAME**: The name of your S3 bucket (e.g., `gnosis-metadata-store`)

## Setting up GitHub Secrets

1. Go to your repository on GitHub
2. Click on "Settings" tab
3. Navigate to "Secrets and variables" → "Actions"
4. Click "New repository secret"
5. Add each of the secrets listed above

## Why S3 Storage?

GitHub Actions artifacts are only available within the same workflow run. To persist the vector store metadata between different workflow runs, we use S3 storage which provides persistent storage accessible across all workflow runs.

## Metadata File

The workflow stores and retrieves `.deepgram/vectorstore_metadata.json` which tracks:

- Which files have been uploaded to the OpenAI vector store
- File checksums to detect changes
- Upload timestamps

This allows the vectorize script to only upload new or modified files, making the process more efficient.

## Compatibility Note

The `s3go-action` is designed for AWS S3, but we're using it with Tigris (S3-compatible storage) by passing the `AWS_ENDPOINT_URL` environment variable. This should work as long as the action respects AWS SDK environment variables. If you encounter issues, you may need to:

1. Use AWS S3 directly instead of Tigris
2. Fork and modify the action to explicitly support custom endpoints
3. Revert to using AWS CLI commands as an alternative
