# Security Rules:

# Terraform Security Rules - GCP (OWASP Top 10)

*   **A01: Broken Access Control**
    *   Avoid primitive roles (`roles/owner`, `roles/editor`); enforce least privilege with predefined/custom IAM roles.
    *   Isolate Dev/Staging/Prod environments into separate GCP Projects.

*   **A02: Cryptographic Failures**
    *   Use CMEK (Cloud KMS) for highly sensitive storage (GCS, Cloud SQL, GCE Disks).
    *   Enforce modern SSL policies and HTTPS redirects for Cloud Load Balancing.
    *   Never hardcode credentials; use Google Secret Manager or environment variables.

*   **A03: Injection**
    *   Sanitize external variables used in `local-exec` or `remote-exec` provisioners.
    *   Validate and sanitize external inputs passed to `metadata_startup_script`.

*   **A04: Insecure Design**
    *   Deploy databases (Cloud SQL, Memorystore) in private networks (`ipv4_enabled = false`).
    *   Enforce default-deny firewall rules; never expose SSH/RDP to `0.0.0.0/0`.

*   **A05: Security Misconfiguration**
    *   Enable `uniform_bucket_level_access` and `public_access_prevention` on GCS buckets.
    *   Disable auto-created subnets (`auto_create_subnetworks = false`) and do not use the `default` VPC.
    *   Strictly restrict IAM access to the GCS bucket storing the `terraform.tfstate`.

*   **A06: Vulnerable & Outdated Components**
    *   Pin versions for Terraform core and GCP providers (e.g., `~> 5.0`).
    *   Use verified modules and pin Git sources to specific commit hashes or tags.

*   **A07: Identification & Authentication Failures**
    *   Do not export Service Account keys; mandate Workload Identity Federation for CI/CD.
    *   Assign dedicated Service Accounts to compute resources instead of the default SA.

*   **A08: Software & Data Integrity Failures**
    *   Enable object versioning on the GCS state bucket to prevent accidental overwrite or tampering.
    *   Mandate IaC static security scans (e.g., `tfsec`, `checkov`, `kics`) prior to deployment.

*   **A09: Security Logging & Monitoring Failures**
    *   Enable VPC Flow Logs, GCS Access Logs, and Cloud Load Balancing Logs.
    *   Provision Cloud Monitoring alert policies (`google_monitoring_alert_policy`) for critical infrastructure.

*   **A10: Server-Side Request Forgery (SSRF)**
    *   Disable legacy metadata endpoints on Compute Engine (`metadata = { "disable-legacy-endpoints" = "true" }`).
    *   Restrict egress firewall rules to explicitly allowed external IPs and services.