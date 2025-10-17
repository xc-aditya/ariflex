# API Documentation

This API provides proxy management services for devices. All endpoints require a `device_id` to identify the requesting device.

## Base URL
```
https://ariiflexapp.vercel.app/
```

## Authentication
No authentication required. Requests are identified by `device_id` in the request body.

## Common Headers
```
Content-Type: application/json
```

## Endpoints

### POST /api/init
Initializes a device and retrieves configuration information.

#### Request Body
```json
{
  "device_id": "string"
}
```

#### Success Response (200)
```json
{
  "alerts": [
    {
      "id": "number",
      "message": "string",
      "active": true
    }
  ],
  "app_config": {
    "min_version_code": "number",
    "latest_version_code": "number"
  },
  "min_version_code": "number",
  "latest_version_code": "number",
  "user_ip": "string",
  "isbanned": false,
  "ban_reason": null
}
```

#### Banned Response (200)
```json
{
  "isbanned": true,
  "ban_reason": "string",
  "user_ip": "string"
}
```

#### Error Responses
- **400 Bad Request**: Missing `device_id`
  ```json
  {
    "error": "Missing device_id"
  }
  ```
- **405 Method Not Allowed**: Invalid HTTP method
  ```json
  {
    "error": "Method not allowed"
  }
  ```
- **500 Internal Server Error**: Server error
  ```json
  {
    "error": "Internal server error"
  }
  ```

### POST /api/start
Requests a proxy assignment for the device. If the device already has an assigned proxy, returns the existing one. Proxies are automatically released after 1 hour of inactivity.

#### Request Body
```json
{
  "device_id": "string"
}
```

#### Success Response (200)
```json
{
  "proxy_ip": "string",
  "proxy_port": "number",
  "proxy_username": "string",
  "proxy_password": "string"
}
```

#### Error Responses
- **400 Bad Request**: Missing `device_id`
  ```json
  {
    "error": "Missing device_id"
  }
  ```
- **405 Method Not Allowed**: Invalid HTTP method
  ```json
  {
    "error": "Method not allowed"
  }
  ```
- **500 Internal Server Error**: Server error
  ```json
  {
    "error": "Internal server error"
  }
  ```

#### Special Cases
- **No Proxy Available (200)**: When no proxies are available in the pool
  ```json
  {
    "error": "No proxy available"
  }
  ```

### POST /api/stop
Releases a proxy back to the available pool.

#### Request Body
```json
{
  "device_id": "string",
  "proxy_ip": "string",
  "proxy_port": "number"
}
```

#### Success Response (200)
```json
{
  "success": true
}
```

#### Error Responses
- **400 Bad Request**: Missing required fields (`device_id`, `proxy_ip`, `proxy_port`)
  ```json
  {
    "error": "Missing required fields"
  }
  ```
- **404 Not Found**: Proxy not found or not assigned to the requesting IP
  ```json
  {
    "error": "Proxy not found or not assigned to this IP"
  }
  ```
- **405 Method Not Allowed**: Invalid HTTP method
  ```json
  {
    "error": "Method not allowed"
  }
  ```
- **500 Internal Server Error**: Server error
  ```json
  {
    "error": "Internal server error"
  }
  ```

## Notes
- All endpoints only accept POST requests
- Device IDs should be unique strings generated and saved on the app's first launch
- Proxy assignments are managed automatically and released after inactivity
- Ban status is checked on initialization
- All responses include appropriate HTTP status codes
