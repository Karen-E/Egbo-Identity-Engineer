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


## Update: creating multiple users

The script was extended from creating one hardcoded user to creating several in a single run.

**Data structure:** The single user dictionary was replaced with a list of dictionaries. Square brackets form the list, and each user's nested `profile` dictionary sits in its own curly braces, separated by commas.

**Looping:** A `for` loop now iterates through the list. The `url` and `headers` are built once before the loop since they are identical for every request. The `requests.post()` call and the status-code check sit inside the loop, because Okta's create endpoint accepts one user per request.

**Bug caught during review:** The first version passed the entire list to the request (`json=three_members`) instead of the current user (`json=user`). Okta expects a single `{"profile": {...}}` object per request, so sending the whole list would have been the wrong shape, and the loop would have repeated the same bundled request each pass. Changing it to `json=user` sends one user per request.

**Result:** All three test users were created successfully, confirmed in both the terminal output and the Okta Admin Console.


**Duplicate-run test:** Running the script a second time returned `400 Bad Request` for all three users, with the message `login: An object with this field already exists in the current organization`. Okta accepted the request and authenticated the token, but rejected each one because logins must be unique within the tenant. This differs from the earlier `401` (Okta didn't accept the credentials) in that the problem here was the request's data, not authentication.

Because the `else` branch only prints the error and has no `break`, the loop continued through all three users. This is usually the right behavior for batch jobs, since one failure shouldn't block the rest.

**Known limitation:** The error output does not identify which user failed. Printing the user's email in the `else` branch would fix this.
