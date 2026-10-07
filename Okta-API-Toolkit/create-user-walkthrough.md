# create_user.py — Technical Walkthrough

`create_user.py` is a Python script that creates a new user in an Okta tenant using the Okta API.

The setup mirrors `list_users.py`: the `os`, `requests`, and `python-dotenv` libraries are imported, `load_dotenv()` loads the `.env` file, and the domain and API token are pulled into variables. From there, three things are new.

**1. The new user's profile data**

Okta requires user data to follow a specific, nested format. The new user's details are stored in a Python dictionary, with the actual profile fields (`firstName`, `lastName`, `email`, `login`) nested inside an outer `profile` key — matching Okta's documented request schema exactly:

```python
new_user = {
    "profile": {
        "firstName": "Tony",
        "lastName": "Washington",
        "email": "twashington@gmail.com",
        "login": "twashington@gmail.com"
    }
}
```

A dictionary is used specifically because each piece of data needs an explicit label — Okta's API expects fields by exact name, not by position. `login` and `email` are kept as separate fields, since Okta treats them as two distinct values even when they're typically identical.

**2. A `Content-Type` header, and `POST` instead of `GET`**

Since this script sends data to Okta rather than only retrieving it, two things change from `list_users.py`: the request uses `requests.post()` instead of `requests.get()`, and a `Content-Type: application/json` header is added so Okta knows how to correctly parse the data being sent.

**3. Checking for a second status code**

Okta's create-user endpoint returns `201 Created` on success — the standard HTTP code for a resource being created — rather than the generic `200 OK` returned by read-only `GET` requests. The script checks for either code to correctly detect success.

**Result:** the script ran successfully on the first attempt, creating a new user with status `PROVISIONED` — the expected initial state for an API-created user who hasn't yet completed first login.
