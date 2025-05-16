# Rise Coding Interview - React Native

## Welcome!

In the next hour, we'd like to see you build a small React Native app from scratch. At the end of our time together, we should have a mobile app that can run in the Android simulator.

This is a paired, "open-book" coding assessment. We'd like you to share your screen and have your camera on.

### 👍 You may:
- Google for API or syntax, search for how-tos and examples
- Use AI coding tools and IDEs (we'd love to hear why you like the tools you use)
- Use open-source libraries and frameworks
- Ask us for clarification

### 🙅 You may not:
- Re-use old work
- Get help from another individual

### 🏁 To begin:
- Clone this repo and create a branch
- Select a framework (or don't). Be prepared to explain your choices.
- Select a UI framework (or don't). Be prepared to explain your choices.
- Include unit testing with Jest. We're not looking for 100% coverage, but we'd like to see your approach to testing.

### 📋 Your app should:
- Display a splash screen with the Rise Logo
- Start with an unauthenticated screen that prompts users to log in with a username and password.
- Once authenticated, your app should display three tabs:
  - HomeTab:
    - Card with decoded JWT token, displaying `user_email` at top
    - Have a card that shows current GPS location
  - Empty Tab
    - It should be empty!
  - Profile Tab
    - Display additional information returned from this API end point
      - A card with user's name and title
      - A working log-out button
      - Button to enrol biometrics

## Relevant APIs:

### Test Credentials
- `j.d.@example.com` / `FakePassword1`

---

### 📘 `POST /tokens/with_refresh_token`

Authenticate a user and retrieve an access token and refresh token for subsequent authorized requests.

---

#### 📍 **Endpoint**

```
POST https://gateway-test.risepeople.com/tokens/with_refresh_token
```

---

#### 📥 **Headers**

| Header           | Value               | Description                              |
|------------------|---------------------|------------------------------------------|
| `Accept`         | `application/json`  | Client expects a JSON response           |
| `Content-Type`   | `application/json`  | Request body is formatted as JSON        |

---

#### 🧾 **Request Body**

Send a JSON object containing the user’s credentials:

```json
{
  "email": "j.d@example.com",
  "password": "FakePassword1",
  "realm": "rise"
}
```

#### Field Descriptions:

| Field      | Type     | Description                                  |
|------------|----------|----------------------------------------------|
| `email`    | string   | The user's email address                     |
| `password` | string   | The user's password (in plain text)          |
| `realm`    | string   | The realm or tenant (e.g., `"rise"`)         |

---

#### 📤 **Expected Response**

```json
{
  "access_token": "string",
  "refresh_token": "string",
  "expires_in": 3600,
  "token_type": "Bearer"
}
```

#### Response Field Descriptions:

| Field           | Type     | Description                              |
|------------------|----------|------------------------------------------|
| `access_token`   | string   | Token to be used in authenticated requests |
| `refresh_token`  | string   | Token used to refresh the access token     |
| `expires_in`     | number   | Expiration time of the access token (seconds) |
| `token_type`     | string   | Type of token (usually `"Bearer"`)         |

---

#### 🔒 **Security Notes**
- This endpoint transmits **sensitive credentials**, so ensure the request is made over **HTTPS**.
- Do not log or expose the `password`, `access_token`, or `refresh_token`.

---

#### ✅ **Use Cases**

- Logging in a user via CLI or script
- Backend service authentication
- Testing login functionality with credentials

---

### 📘 `GET /employees`

Retrieve a list of employees, including specific fields and optional related data (such as employee photos and current status).

---

#### 📍 **Endpoint**

```
GET https://gateway-test.risepeople.com/employees?include=employee_photo&fields[employees]=name,employee_photo,current_status
```

---

#### 📥 **Headers**

| Header         | Value                                             | Description                                  |
|----------------|---------------------------------------------------|----------------------------------------------|
| `Accept`       | `application/json, text/plain, */*`               | Accepts JSON or other media types            |
| `Authorization`| `Bearer <JWT>`                                    | Bearer token used to authenticate the request|

> 🔐 The actual JWT token is passed directly in the request. In production docs, you should **redact or obfuscate it** for security.

---

#### 🧾 **Query Parameters**

| Parameter                      | Type     | Description                                                                 |
|--------------------------------|----------|-----------------------------------------------------------------------------|
| `include=employee_photo`       | string   | Requests inclusion of the related `employee_photo` resource                |
| `fields[employees]=name,employee_photo,current_status` | string | Restricts the fields returned for each employee object                     |

This pattern follows the [JSON:API](https://jsonapi.org/) spec for sparse fieldsets and resource inclusion.

---

#### 🧪 **Sample Response**

```json
{
  "data": [
    {
      "id": "1234",
      "type": "employee",
      "attributes": {
        "name": "Jane Doe",
        "employee_photo": {
          "url": "https://cdn.example.com/photos/1234.jpg"
        },
        "current_status": "Active"
      }
    }
  ],
  "included": [
    {
      "type": "employee_photo",
      "id": "photo-1234",
      "attributes": {
        "url": "https://cdn.example.com/photos/1234.jpg"
      }
    }
  ]
}
```

---

#### 📤 **Response Fields**

| Field            | Type     | Description                             |
|------------------|----------|-----------------------------------------|
| `name`           | string   | Full name of the employee               |
| `employee_photo` | object   | Photo resource associated with employee |
| `current_status` | string   | Employment status (e.g., Active, Terminated) |

---

#### 🔒 **Authentication**

- Requires a **valid JWT** passed in the `Authorization` header.
- The token must contain scopes/permissions to access employee data.

---

#### ✅ **Use Cases**

- Displaying a directory of employees
- Loading profile photos and statuses in a company dashboard
- Rendering filtered employee lists in apps or reports
