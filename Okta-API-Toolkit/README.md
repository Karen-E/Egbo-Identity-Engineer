# Okta API Toolkit

## What this project covers
A set of Python scripts that interact directly with the Okta API to perform core identity lifecycle operations — listing, creating, deactivating users, resetting MFA factors, and assigning application access. Built to demonstrate hands-on API-level IAM skills beyond the Okta Admin Console UI.

## What's in this folder
- `list_users.py` — retrieves and prints all users in the Okta tenant via the `/api/v1/users` endpoint
<!-- add a line for each additional script as it's completed, e.g.: -->
<!-- - `create_user.py` — creates a new user via the Okta API -->

## Skills demonstrated
- Authenticating to the Okta API using a bearer-style token (`SSWS` scheme) via custom HTTP headers
- Securely managing API credentials using environment variables (`.env`) excluded from version control via `.gitignore`
- Making HTTP GET requests with the Python `requests` library and handling JSON responses
- Reading and interpreting Okta API error responses (status codes, error codes, error summaries)
- Real-world environment troubleshooting: resolving Python environment/interpreter mismatches, malformed URL construction, and expired API token rotation

## Why I built this
<!-- your own words -->

## What I learned
<!-- your own words — this is what you're about to send me to review -->

## What I'd do differently
<!-- your own words -->

## Tools & technologies used
- Python 3.14
- `requests` library
- `python-dotenv`
- Okta API (REST)
- Git / GitHub
- VS Code

## Related projects in this portfolio
- [OIDC-Labs](../OIDC-Labs) — OIDC application configuration and policy testing
- [SAML-Labs](../SAML-Labs) — SAML SSO configuration and troubleshooting
- [SCIM-Labs](../SCIM-Labs) — SCIM provisioning setup and connection troubleshooting
