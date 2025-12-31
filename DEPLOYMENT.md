# Deployment Setup Guide

## Current Issue

Your GitHub Actions workflow is failing with: `Error: Failed to authenticate, have you run firebase login?`

This is because you have Workload Identity Federation (WIF) partially configured but not fully set up in Google Cloud.

## Why You're Seeing This Error

The workflow is trying to use **Workload Identity Federation** (the secure, no-secrets approach), but the Google Cloud side isn't fully configured to allow GitHub Actions to authenticate.

## Quick Fix (Choose One)

### Option A: Simple Method (Using Service Account Key)

**Pros:** Easy setup, works immediately
**Cons:** Requires storing a secret, less secure than WIF

1. **Delete the conflicting workflows:**
   ```bash
   rm .github/workflows/deploy.yml
   rm .github/workflows/deploy-fixed.yml
   ```

2. **Keep only firebase-hosting.yml**

3. **Generate and add the service account key:**
   ```bash
   # In your Firebase project console:
   # 1. Go to Project Settings > Service Accounts
   # 2. Click "Generate New Private Key"
   # 3. Save the JSON file
   ```

4. **Add to GitHub Secrets:**
   - Go to your GitHub repo > Settings > Secrets and variables > Actions
   - Create new secret: `FIREBASE_SERVICE_ACCOUNT_ANGELICAREAMS_PROD`
   - Paste the entire JSON content from step 3

5. **Push to trigger deployment**

---

### Option B: Secure Method (Workload Identity Federation) - RECOMMENDED

**Pros:** No secrets stored, more secure, Google's recommended approach
**Cons:** Requires Google Cloud Console setup (15 minutes)

This is the approach your `deploy.yml` is trying to use. You need to complete the GCP setup:

#### Step 1: Complete Google Cloud Setup

Your workflow already has these values configured:
- Workload Identity Pool: `projects/127969245284/locations/global/workloadIdentityPools/github-pool`
- Provider: `github-provider`
- Service Account: `github-deploy@angelicareams-prod.iam.gserviceaccount.com`

You need to verify/configure the bindings in Google Cloud Console:

```bash
# 1. Install Google Cloud CLI if you haven't
brew install google-cloud-sdk

# 2. Login to Google Cloud
gcloud auth login

# 3. Set your project
gcloud config set project angelicareams-prod

# 4. Grant the service account Firebase Hosting permissions
gcloud projects add-iam-policy-binding angelicareams-prod \
  --member="serviceAccount:github-deploy@angelicareams-prod.iam.gserviceaccount.com" \
  --role="roles/firebasehosting.admin"

# 5. Allow GitHub Actions to impersonate the service account
# Replace YOUR_GITHUB_USERNAME and YOUR_REPO_NAME
gcloud iam service-accounts add-iam-policy-binding \
  github-deploy@angelicareams-prod.iam.gserviceaccount.com \
  --project=angelicareams-prod \
  --role="roles/iam.workloadIdentityUser" \
  --member="principalSet://iam.googleapis.com/projects/127969245284/locations/global/workloadIdentityPools/github-pool/attribute.repository/YOUR_GITHUB_USERNAME/AngelicaReams"
```

#### Step 2: Update Your Workflow

```bash
# Delete the old files
rm .github/workflows/firebase-hosting.yml

# Rename the fixed one
mv .github/workflows/deploy-fixed.yml .github/workflows/deploy.yml
```

#### Step 3: Test

Push to `main` branch and the deployment should work without any secrets!

---

## Security Comparison

| Method | Security Level | Setup Difficulty | Maintenance |
|--------|---------------|------------------|-------------|
| Service Account Key (Option A) | Medium | Easy | Need to rotate keys |
| Workload Identity Federation (Option B) | High | Medium | Zero maintenance |

## Current Workflow Status

You currently have **TWO workflows** that will conflict:
- `.github/workflows/deploy.yml` - Uses WIF (not fully configured)
- `.github/workflows/firebase-hosting.yml` - Uses service account key (missing secret)

**You must delete one of them** before deployment will work.

## Recommendation

For a production website, I recommend **Option B (Workload Identity Federation)**. It's Google's recommended approach and more secure since:
- No long-lived credentials stored in GitHub
- Automatic credential rotation
- Better audit logging
- No risk of leaked secrets

The initial setup takes 15 minutes but you'll never have to worry about it again.

## Need Help?

If you get stuck with the GCP setup, let me know and I can help troubleshoot the specific error messages.
