# list_users.py — Technical Walkthrough

`list_users.py` is a Python script that retrieves and prints a list of users (by email) from an Okta tenant using the Okta API.

There were three major pieces required to make this work.

**1. Environment loading**

The API token and domain are kept out of the script itself and stored in a separate `.env` file, which is excluded from version control via `.gitignore`. This prevents the token from ever being exposed in a public repository.

To use the contents of `.env`, the `python-dotenv` library must be installed. From it, the `load_dotenv()` function is imported and called, which reads `.env` and makes its contents available to the script. Two variables are then created to hold the values needed: one for the domain, one for the API token.

**2. Building the request header**

Every request sent to the Okta API needs an `Authorization` header to prove it's a legitimate, permitted request. Okta uses its own authentication scheme, `SSWS`, followed by the API token. This header is what allows Okta to verify the request's credentials and determine what permissions it has before processing it.

**3. Handling the response**

After the request is sent, the script checks the response's status code using an if/else statement. If the status code is `200`, the request succeeded, and the script prints Okta's returned list of users. If not, the script prints the status code and Okta's error message instead. This prevents the script from crashing on failure and makes it clear, either way, what happened.
