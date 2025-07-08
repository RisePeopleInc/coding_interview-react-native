# Rise Coding Interview - React Native

## Welcome!

In the next hour, we'd like to see you build a small React Native app from scratch. At the end of our time together, we should have a mobile app that can run in the Android simulator.

This is a paired, "open-book" coding assessment. We'd like you to share your screen and have your camera on.

### 👍 You may:
- Google for API or syntax, search for how-tos and examples
- Use AI coding tools and IDEs (we'd love to hear why you like the tools you use)
- Use open-source libraries and frameworks
- Ask us for clarification

### 🙅 You may <ins>not</ins>:
- Re-use old work
- Get help from another individual

### 🏁 To begin:
- Clone this repo and create a branch
- Select a framework (or don't). Be prepared to explain your choices.
- Select a UI framework (or don't). Be prepared to explain your choices.
- Our Coding Standards include unit testing with Jest. We're not looking for 100% coverage, but we'd like to see your approach to testing.

### Tech Prerequisites: 
The app will need to be able to perform / interact with the following :
- Public API endpoints that require no authentication.
- Public API endpoints that require JWT token.
- Device's GPS data.
- Biometric data.

### 📋 Your app should:
- Display a splash screen with the Rise Logo
- Start with an unauthenticated screen that prompts users to log in with a username and password. The API (token) response will be used throughout the app.
- Once authenticated, your app should have a layout that has a Tabs at the bottom of the screen:
  - HomeTab(Initial Tab) - A screen with Two Cards:
    - a Card with decoded JWT token information where the CardTitle displays the `user_email` at top, and the content shows the `uta` payload.
    - a Card with a GPS payload from the device.
  - Empty Tab
    - A screen that shows no content.
  - Profile Tab
    - Display additional information returned from the `Employeees` API end point
      - A card with user's name and title
      - A working log-out button
      - Button to enrol biometrics

## APIs:

### Test Credentials
- id: `j.d.@example.com`
- password: `FakePassword1`

---

### 📘 Login API:  POST `/tokens/with_refresh_token`

Authenticate a user and retrieve an access token and refresh token for subsequent authorized requests.

---

#### 📍 **Endpoint**

```
https://gateway-test.risepeople.com/tokens/with_refresh_token
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
  "auth_token": "string",
  "refresh_token": "string"
}
```

#### Response Field Descriptions:

| Field            | Type     | Description                               |
|------------------|----------|-------------------------------------------|
| `auth_token`     | string   | Token to be used in authenticated requests|
| `refresh_token`  | string   | Token used to refresh the access token    |

---

#### 🔒 **Security Notes**
- This endpoint transmits **sensitive credentials**, so ensure the request is made over **HTTPS**.
- Do not expose the `password`, `access_token`, or `refresh_token` to the UI.

---

#### ✅ **Use Cases**

- Logging in a user via CLI or script
- Backend service authentication
- Testing login functionality with credentials

---

### 📘 Employee API: GET `/employees/:id`

Retrieve detailed information about a specific employee, including their organizational structure, assignments, and relationships.

---

#### 📍 **Endpoint**

```
https://gateway-test.risepeople.com/employees/{id}?include=departments,teams,locations,primary_team,primary_department,primary_location,primary_manager,managers,employee_photo,employment_positions,organization&fields[departments]=name&fields[teams]=name&fields[locations]=name
```

---

#### 📥 **Headers**

| Header           | Value                                 | Description                                       |
|------------------|---------------------------------------|---------------------------------------------------|
| `Accept`         | `application/json, text/plain, */*`   | Expected response format                          |
| `Authorization`  | `<JWT>`                               | Auth_Token from login API for authorization       |

> ⚠️ Do not include the `Bearer` word in the Authorization Header, just use the token value instaed.

---

#### 🔍 **Query Parameters**

| Parameter                       | Type     | Description                                                       |
|---------------------------------|----------|-------------------------------------------------------------------|
| `include`                       | string   | Comma-separated list of related resources to include              |
| `fields[departments]=name`      | string   | Restrict department fields to only include the `name` attribute   |
| `fields[teams]=name`            | string   | Restrict team fields to only include the `name` attribute         |
| `fields[locations]=name`        | string   | Restrict location fields to only include the `name` attribute     |

##### Included Resources:

- `departments`, `teams`, `locations`
- `primary_team`, `primary_department`, `primary_location`
- `primary_manager`, `managers`
- `employee_photo`
- `employment_positions`
- `organization`

---

#### 🧪 **Sample Response Structure**

```json
{
  "data": {
    "id": "295029",
    "type": "employee",
    "attributes": {
      "name": "John Doe",
      "current_status": "Active",
      "title": "Current Title",
      ...
    },
    "relationships": {
      "departments": { "data": [...] },
      "primary_manager": { "data": { "id": "1234", "type": "manager" } },
      ...
    }
  },
  "included": [
    {
      "type": "department",
      "id": "dept-1",
      "attributes": { "name": "Engineering" }
    },
    {
      "type": "team",
      "id": "team-1",
      "attributes": { "name": "Mobile" }
    },
    ...
  ]
}
```

---


#### ✅ **Use Cases**

- Viewing an employee's full profile and org chart context
- Admin panels and management dashboards
- Auditing employee assignments and relationships
