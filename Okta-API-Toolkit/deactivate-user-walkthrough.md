# deactivate_user.py — Technical Walkthrough

`deactivate_user.py` is a Python script that deactivates one or more users in an Okta tenant, starting from their email addresses.

The setup matches the earlier scripts: the `os`, `requests`, and `python-dotenv` libraries are imported, `load_dotenv()` loads the `.env` file, and the domain and API token are pulled into variables. The users to deactivate are kept in a list of email addresses.

**1. Why two requests are needed**

Okta's deactivate endpoint requires a user's ID, but the script starts with an email. For each email, the script first sends a `GET` request to look the user up. The response contains the user's full record, including the `id`. That ID is then used to build the URL for a second request, a `POST` to `/api/v1/users/{id}/lifecycle/deactivate`.

`GET` is used for the lookup because it only reads data. `POST` is used for the deactivation because it changes the user's state.

**2. No request body, no `Content-Type` header**

Neither request sends a body. The deactivate endpoint only needs to be told to perform an action, so the `Content-Type` header used in `create_user.py` is not needed here.

**3. Status checks on both requests**

- The lookup is checked for `200`. A `404` means Okta found no user with that email. This was confirmed in a test using a nonexistent address, and it shows that the script reports the problem for that user and does not build a deactivate URL.
- The deactivate request has its own check for `200`. Without it, the script would print a success message regardless of Okta's reply, which in an offboarding workflow could make an account look closed when it isn't.

Failures print the email, the status code, and Okta's response text, so it's clear which user failed and why.

**Scope and safety:** Deactivation was treated as irreversible on this tenant, based on earlier testing. The script was therefore built in two stages, with the lookup tested alone first and the deactivate step added only after the lookup was confirmed to find the right users. Only disposable test users were targeted.

**Result:** Two test users were deactivated. The result was confirmed in the terminal output and in the Okta Admin Console, and the other test users were unchanged.
