# API Documentation

## Overview

The LiveKit AI Car Call Centre provides REST APIs for LiveKit token generation and room management.

## Base URL

- Development: `http://localhost:5001`
- Production: Configured via reverse proxy

## Authentication

Currently, the API uses no authentication for development. In production, implement proper authentication (JWT, API keys, or OAuth).

## Endpoints

### Get LiveKit Access Token

Generates a LiveKit access token for a user to join a room.

**Endpoint**: `GET /api/getToken`

**Query Parameters**:
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| name | string | Yes | User's display name |
| room | string | No | Room name (auto-generated if not provided) |

**Response**: `200 OK` - Returns JWT token as plain text

**Example Request**:
```
GET /api/getToken?name=John%20Doe&room=support-room-123
```

**Example Response**:
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ2aWRlbyI6eyJyb29tX2pvaW4iOnRydWUsInJvb20iOiJzdXBwb3J0LXJvb20tMTIzIn0sImlkZW50aXR5IjoiSm9obiBEb2UiLCJuYW1lIjoiSm9obiBEb2UiLCJleHAiOjE3MDAwMDAwMDB9.signature
```

**Error Responses**:
| Status | Description |
|--------|-------------|
| 400 | Missing required parameter `name` |
| 500 | Internal server error (LiveKit API failure) |

### List LiveKit Rooms (via LiveKit API)

Lists all active rooms on the LiveKit server. This is an internal helper function, not a public endpoint.

**Function**: `get_rooms()` in `backend/server.py`

**Returns**: Array of room names

**Example**:
```python
rooms = await get_rooms()
# Returns: ["room-abc123", "room-def456", "support-room-123"]
```

### Generate Room Name (Internal)

Generates a unique room name using UUID.

**Function**: `generate_room_name()` in `backend/server.py`

**Returns**: String like `room-a1b2c3d4`

**Note**: Ensures uniqueness by checking against existing rooms.

## LiveKit Integration

### Token Structure

The generated JWT token contains:
- **VideoGrants**: 
  - `room_join: true` - Allow joining rooms
  - `room: <room_name>` - Specific room to join
- **Identity**: User's name (from `name` parameter)
- **Name**: User's display name

### Frontend Integration

In `frontend/src/components/LiveKitModal.jsx`:

```javascript
const response = await fetch(`/api/getToken?name=${encodeURIComponent(userName)}`);
const token = await response.text();
```

Then used with `LiveKitRoom` component:

```jsx
<LiveKitRoom
  serverUrl={import.meta.env.VITE_LIVEKIT_URL}
  token={token}
  connect={true}
  video={false}
  audio={true}
>
  <RoomAudioRenderer />
  <SimpleVoiceAssistant />
</LiveKitRoom>
```

## Environment Variables Required

| Variable | Description |
|----------|-------------|
| `LIVEKIT_API_KEY` | LiveKit API key |
| `LIVEKIT_API_SECRET` | LiveKit API secret |
| `VITE_LIVEKIT_URL` | LiveKit WebSocket URL (frontend) |

## Error Handling

### Common Errors

1. **Missing Credentials**
   - Ensure `.env` has `LIVEKIT_API_KEY` and `LIVEKIT_API_SECRET`
   - Check LiveKit dashboard for valid credentials

2. **Network Errors**
   - Verify LiveKit server is reachable
   - Check firewall/proxy settings

3. **Token Expiry**
   - Tokens expire after 1 hour (default)
   - Re-fetch token on reconnection

## Rate Limiting

No rate limiting implemented in development. Add in production:
- Flask-Limiter for API rate limiting
- LiveKit server-side limits

## Testing with curl

```bash
# Get token
curl "http://localhost:5001/api/getToken?name=TestUser"

# With room
curl "http://localhost:5001/api/getToken?name=TestUser&room=my-room"
```

## Frontend API Calls

All frontend API calls go through Vite proxy (`/api/*` → `http://localhost:5001/*`):

```javascript
// In vite.config.js
server: {
  proxy: {
    "/api": {
      target: "http://localhost:5001",
      changeOrigin: true,
      rewrite: (path) => path.replace(/^\/api/, "")
    }
  }
}
```

## LiveKit Server Configuration

The frontend connects to LiveKit via WebSocket:

```javascript
// In LiveKitModal.jsx
serverUrl={import.meta.env.VITE_LIVEKIT_URL}
// e.g., wss://your-livekit-server.com
```

Ensure the LiveKit server:
1. Is accessible from the frontend origin
2. Has proper CORS headers for WebSocket
3. Matches the API key/secret used in backend

## Future API Endpoints (Planned)

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/api/cars` | GET | List all cars in database |
| `/api/cars/:vin` | GET | Get car details by VIN |
| `/api/cars` | POST | Create new car entry |
| `/api/support/sessions` | GET | List support sessions |
| `/api/support/sessions/:id` | GET | Get session details/transcript |

## Related Files

- `backend/server.py` - Main API implementation
- `backend/api.py` - LiveKit API helpers
- `frontend/src/components/LiveKitModal.jsx` - Frontend token fetching
- `.env.example` - Environment variable template