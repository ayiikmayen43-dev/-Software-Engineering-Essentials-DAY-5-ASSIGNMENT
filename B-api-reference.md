# B. API Reference Entry: Create a Project Task

## Overview

Creates a new task inside a project management application. The caller must be authenticated and must have permission to create tasks in the specified project. The new task is assigned to an existing user and includes a due date and priority.

## Endpoint

- **HTTP method:** `POST`
- **Path:** `/v1/projects/{projectId}/tasks`
- **Success status:** `201 Created`
- **Content type:** JSON

## Path Parameters

| Name | Data type | Required | Description |
|---|---|---:|---|
| `projectId` | string (UUID) | Yes | Unique identifier of the project in which the task will be created. |

## Query Parameters

This endpoint does not accept query parameters.

## Request Headers

| Header | Required | Description |
|---|---:|---|
| `Authorization` | Yes | Bearer access token. Format: `Bearer <access_token>`. |
| `Content-Type` | Yes | Must be `application/json`. |
| `Accept` | Yes | Indicates that the client expects a JSON response. Use `application/json`. |

## Request Body

Send a JSON object with the following fields:

| Name | Data type | Required | Description |
|---|---|---:|---|
| `title` | string | Yes | Task title. Must contain 1–120 characters after trimming whitespace. |
| `description` | string | No | Additional task details. Maximum length is 2,000 characters. If omitted, the description is stored as `null`. |
| `assigneeId` | string (UUID) | Yes | ID of an existing user who will be assigned the task. The user must belong to the project. |
| `dueDate` | string (date, ISO 8601) | Yes | Due date in `YYYY-MM-DD` format. |
| `priority` | string (enum) | Yes | Task priority. Allowed values: `low`, `medium`, or `high`. |

### Example Request

```http
POST /v1/projects/7f3a2c10-8b4d-4f1a-9c2e-6d5b1a0f1234/tasks HTTP/1.1
Host: api.example.com
Authorization: Bearer eyJhbGciOiJIUzI1NiJ9.example-token
Content-Type: application/json
Accept: application/json
```

```json
{
  "title": "Prepare project presentation",
  "description": "Create the slides and review them with the project team.",
  "assigneeId": "2c6f9a21-5d43-4a90-8b12-7e6f5a4b3210",
  "dueDate": "2026-12-15",
  "priority": "high"
}
```

## Successful Response

### `201 Created`

The task was created successfully. The response contains the saved task, including its generated ID and creation timestamp.

### Example Response Body

```json
{
  "id": "9a8b7c6d-5e4f-4321-9876-1a2b3c4d5e6f",
  "projectId": "7f3a2c10-8b4d-4f1a-9c2e-6d5b1a0f1234",
  "title": "Prepare project presentation",
  "description": "Create the slides and review them with the project team.",
  "assigneeId": "2c6f9a21-5d43-4a90-8b12-7e6f5a4b3210",
  "dueDate": "2026-12-15",
  "priority": "high",
  "status": "open",
  "createdAt": "2026-11-23T07:10:00Z"
}
```

## HTTP Response Codes

| Status code | Meaning | When it occurs |
|---|---|---|
| `201 Created` | Task created | The request is valid and the task has been saved. |
| `400 Bad Request` | Invalid request | The JSON is malformed, a required field is missing, or a field has the wrong data type or format. |
| `401 Unauthorized` | Authentication required | The access token is missing, invalid, or expired. |
| `403 Forbidden` | Permission denied | The authenticated user does not have permission to create tasks in this project. |
| `404 Not Found` | Resource not found | The project or specified assignee does not exist. |
| `409 Conflict` | Request conflicts with current state | The project is archived or otherwise does not accept new tasks. |
| `415 Unsupported Media Type` | Unsupported content type | The request's `Content-Type` is not `application/json`. |
| `422 Unprocessable Content` | Validation failed | The fields are correctly formatted, but a business rule is not satisfied—for example, the assignee is not a member of the project or the due date is not acceptable. |
| `429 Too Many Requests` | Rate limit exceeded | The client has sent too many requests within the allowed time window. |
| `500 Internal Server Error` | Server error | An unexpected error occurred while processing the request. |
| `503 Service Unavailable` | Service temporarily unavailable | The API is temporarily unable to handle the request, such as during maintenance. |

## Validation and Behavior Notes

- The `projectId` and `assigneeId` values must be valid UUIDs.
- The title cannot be empty or contain only whitespace.
- The priority value is case-sensitive and must be one of the three documented values.
- The assignee must be a member of the project.
- The API generates the task `id`, sets `status` to `open`, and records `createdAt`.
- The example host `api.example.com` and sample identifiers are illustrative; replace them with real values when integrating with an actual service.

## Design Assumptions

This is a hypothetical API designed for this assignment. The endpoint uses UUID identifiers, requires an assignee and due date, and returns the created task as JSON. The response codes describe the expected behavior of this proposed design.
