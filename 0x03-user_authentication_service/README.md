# User Authentication Service

- 0-User model: user.py contains a SQLAlchemy model named User for a database table named users (by using the mapping declaration of SQLAlchemy) that will have the following attributes:
id, the integer primary key.
email, a non-nullable string.
hashed_password, a non-nullable string.
session_id, a nullable string.
reset_token, a nullable string.
- 1-create user: db.py contains a completion of the DB class provided below to implement the add_user method
- 2-Find user: db.py with the following updates:
   Implements the DB.find_user_by method. This method takes in arbitrary keyword arguments and returns the first row found in the users table as filtered by the method’s input arguments.
   SQLAlchemy’s NoResultFound and InvalidRequestError are raised when no results are found, or when wrong query arguments are passed, respectively.
- 3-update user: db.py with the following updates:
   Implements the DB.update_user method that takes as arguments a required user_id integer, an arbitrary keyword arguments, and returns None.
   The method uses find_user_by to locate the user to update, then will update the user’s attributes as passed in the method’s arguments then commit changes to the database.
- 4-Hash password: auth.py contains a _hash_password method that takes in a password string arguments and returns bytes, which is a salted hash of the input password, hashed with bcrypt.hashpw.
- 5-Register user: auth.py with the following updates:
   Implements the Auth.register_user in the Auth class
   Auth.register_user takes mandatory email and password string arguments and return a User object.
   If a user already exist with the passed email, it raises a ValueError with the message User <user's email> already exists.
   If not, it hashes the password with _hash_password, saves the user to the database using self._db and returns the User object.
- 6-Basic Flask app: app.py contains a basic Flask app that has a single GET route ("/") and use flask.jsonify to return a JSON payload of the form:
{"message": "Bienvenue"}
- 7-Register user: app.py with the following updates:
   Implements the end-point to register a user. Define a users function that implements the POST /users route.
   Import the Auth object and instantiate it at the root of the module
   The end-point expects two form data fields: "email" and "password". If the user does not exist, the end-point  registers it and responds.
   If the user is already registered, it catches the exception and returns a JSON payload and returns a 400 status code.
- 8-Credentials validation: auth.py with the following updates:
   Implements the Auth.valid_login method. It should expect email and password required arguments and return a boolean.
- 9-Generate UUIDs: auth.py with the following updates:
   Implements a _generate_uuid function in the auth module. The function returns a string representation of a new UUID..
- 10-Get session ID: auth.py with the following updates:
   Implements the Auth.create_session method. It takes an email string argument and returns the session ID as a string.
   The method finds the user corresponding to the email, generates a new UUID and stores it in the database as the user’s session_id, then returns the session ID.
- 11-Log in: app.py with the following updates:
   Implements a login function to respond to the POST /sessions route.
   The request is expected to contain form data with "email" and a "password" fields.
   If the login information is incorrect, it uses flask.abort to respond with a 401 HTTP status.
   Otherwise, it creates a new session for the user, stores the session ID as a cookie with key "session_id" on the response and returns a JSON payload.
- 12-Find user by session ID: auth.py with the following updates:
   Implements the Auth.get_user_from_session_id method. It takes a single session_id string argument and returns the corresponding User or None.
   If the session ID is None or no user is found, it returns None. Otherwise it returns the corresponding user.
- 13-Destroy session: auth.py with the following updates:
   Implements Auth.destroy_session. The method takes a single user_id integer argument and returns None.
   The method updates the corresponding user’s session ID to None.
- 14-Log out: app.py with the following updates:
   Implements a logout function to respond to the DELETE /sessions route.
   The request is expected to contain the session ID as a cookie with key "session_id".
   Find the user with the requested session ID. If the user exists destroy the session and redirect the user to GET /. If the user does not exist, respond with a 403 HTTP status.
- 15-User profile: app.py with the following updates:
   Implements a profile function to respond to the GET /profile route.
   The request is expected to contain a session_id cookie. Use it to find the user. If the user exist, respond with a 200 HTTP status and a JSON payload.
   If the session ID is invalid or the user does not exist, respond with a 403 HTTP status.
- 16-Generate reset password token: auth.py with the following updates:
   Implements the Auth.get_reset_password_token method. It takes an email string argument and returns a string.
   Find the user corresponding to the email. If the user does not exist, raise a ValueError exception. If it exists, generate a UUID and update the user’s reset_token database field. Return the token.
- 17-Get reset password token: app.py with the following updates:
   Implements a get_reset_password_token function to respond to the POST /reset_password route.
   The request is expected to contain form data with the "email" field.
   If the email is not registered, respond with a 403 status code. Otherwise, generate a token and respond with a 200 HTTP status and a JSON payload.
- 18-Update password: auth.py with the following updates:
   Implements the Auth.update_password method. It takes reset_token string argument and a password string argument and returns None.
   Uses the reset_token to find the corresponding user. If it does not exist, it raises a ValueError exception.
   Otherwise, it hashes the password and updates the user’s hashed_password field with the new hashed password and the reset_token field to None.
- 19-Update password end-point: app.py with the following updates:
   Implements the update_password function in the app module to respond to the PUT /reset_password route.
   The request is expected to contain form data with fields "email", "reset_token" and "new_password".
   Updates the password. If the token is invalid, it catches the exception and responds with a 403 HTTP code.
   If the token is valid, it responds with a 200 HTTP code and a JSON payload.
- 20-End-to-end integration test: main.py contains one function for each of the following sub tasks:
register_user(email: str, password: str) -> None.
log_in_wrong_password(email: str, password: str) -> None.
log_in(email: str, password: str) -> str.
profile_unlogged() -> None.
profile_logged(session_id: str) -> None.
log_out(session_id: str) -> None.
reset_password_token(email: str) -> str.
update_password(email: str, reset_token: str, new_password: str) -> None.
