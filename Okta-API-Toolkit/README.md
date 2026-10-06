# Okta API Toolkit

## What this project covers
A set of Python scripts that interact directly with the Okta API to perform core identity lifecycle operations — listing, creating, deactivating users, resetting MFA factors, and assigning application access. Built to demonstrate hands-on API-level IAM skills beyond the Okta Admin Console UI.

## What's in this folder
- `list_users.py` — retrieves and prints all users in the Okta tenant via the `/api/v1/users` endpoint
- [list-users-walkthrough.md](list-users-walkthrough.md) — technical walkthrough of how list_users.py works, including environment loading, header construction, and response handling

## Skills demonstrated
- Authenticating to the Okta API using a bearer-style token (`SSWS` scheme) via custom HTTP headers
- Securely managing API credentials using environment variables (`.env`) excluded from version control via `.gitignore`
- Making HTTP GET requests with the Python `requests` library and handling JSON responses
- Reading and interpreting Okta API error responses (status codes, error codes, error summaries)
- Real-world environment troubleshooting: resolving Python environment/interpreter mismatches, malformed URL construction, and expired API token rotation

## Why I built this
<!-- your own words -->

## What I learned

Building and testing `list_users.py` surfaced several real issues, each with its own lesson.

**Typos (`load_env` vs. `load_dotenv`, `respone` vs. `response`):** Minor on their own, but each one broke the script outright. The lesson here is to read error messages carefully — Python's `NameError` messages often suggest the correct name directly, which sped up diagnosis once I knew what to look for.

**Malformed URL from a duplicated `https://`:** My `.env` file stored `OKTA_DOMAIN` with `https://` already included, while my script's code *also* prepended `https://` when building the request URL. The result was a broken URL reading `https://https://domain.com`, which failed with a DNS resolution error. The fix was to store only the bare domain in `.env` and let the script handle the protocol prefix — this matches standard convention for `_DOMAIN`-style environment variables, and it's a pattern I'll apply to every script going forward rather than relearn it each time.

**Expired/missing API token:** The script failed with a `401 Invalid token provided` error. Checking the Okta Admin Console confirmed no active token existed for this project. The fix was generating a new token and updating `.env`. This reinforced that API tokens aren't permanent credentials — they need periodic verification and rotation, especially on a project that's been untouched for a while.

**Two separate Python installations on one machine:** Running the script via VS Code's "Run" button produced a `ModuleNotFoundError` for `requests`, even though the library was confirmed installed via the terminal. Investigating showed VS Code's Run button and the terminal were pointing to two different Python installations. The fix was running the script directly from the terminal with `py list_users.py` instead. The broader lesson: when a module "isn't found" despite being installed, the issue may not be the package at all — it can be *which* Python interpreter is actually running the code.

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
