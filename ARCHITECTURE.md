# Architecture

## Project Overview

LiveKit AI Car Call Centre is a car parts support platform that integrates LiveKit real-time audio/video streaming with a React frontend and Python Flask backend.

## System Components

### Frontend (React + Vite)
- **Framework**: React 18.3 + Vite 6.0
- **Styling**: Custom CSS with design system
- **State Management**: React hooks (useState, useEffect)
- **Routing**: Vite router (implicit via index.html proxy)
- **Components**: 
  - `App.jsx` - Main application shell
  - `LiveKitModal.jsx` - Support modal for live agent connection
  - `SimpleVoiceAssistant.jsx` - Voice assistant UI with LiveKit integration
  - `LiveKitModal` - LiveKit room component wrapper

### Backend (Python Flask + LiveKit API)
- **Framework**: Flask 2.x with LiveKit API integration
- **LiveKit**: Real-time audio/video streaming via `@livekit/components-react`
- **Database**: Custom `DatabaseDriver` (not fully implemented in visible code)
- **API Endpoints**:
  - `/api/getToken` - Generate access token for LiveKit rooms
  - `/api/rooms` - List available rooms (via LiveKit API)

### Data Flow
1. User opens the app → `App.jsx` renders header, hero section, and support button
2. Clicking "Talk to an Agent" → `LiveKitModal` opens with name input
3. User submits name → `getToken()` fetches LiveKit access token
4. Token is used to connect to a LiveKit room via `LiveKitRoom` component
5. Voice assistant (`SimpleVoiceAssistant`) streams audio/video from the room
6. Messages are displayed in the conversation area with agent/user differentiation

### Design System
- **Color Palette**: Indigo/Blue theme (primary: #4F46E5, secondary: #3B82F6)
- **Typography**: Inter/system-ui font stack with proper hierarchy
- **Spacing**: Consistent 4px-8px-16px grid system
- **Components**: Reusable UI components with hover states, loading indicators, and error states

## Directory Structure

```
LiveKit-AI-Car-Call-Centre-main/
├── backend/              # Python backend
│   ├── server.py         # Flask application entry point
│   ├── api.py            # LiveKit API integration
│   ├── db_driver.py      # Database driver (abstract)
│   ├── requirements.txt  # Python dependencies
│   └── sample.env        # Sample environment variables
├── frontend/             # React frontend
│   ├── src/
│   │   ├── App.jsx       # Main app component
│   │   ├── App.css       # Global styles
│   │   ├── index.css     # Base styles
│   │   ├── main.jsx      # Entry point
│   │   ├── components/
│   │   │   ├── LiveKitModal.jsx
│   │   │   └── SimpleVoiceAssistant.jsx
│   │   └── index.html    # HTML template
│   ├── package.json      # Frontend dependencies
│   └── vite.config.js    # Vite configuration
├── docs/                 # Documentation
│   ├── ARCHITECTURE.md
│   ├── DEVELOPMENT.md
│   ├── API.md
│   ├── CHANGELOG.md
│   └── .env.example
├── .env.example          # Environment variable template
└── README.md             # Project overview
```

## Key Features

1. **Live Agent Support** - Real-time voice assistant via LiveKit
2. **Car Parts Lookup** - API integration for car details by VIN
3. **Support Chat** - Modal-based support flow with live agent connection
4. **Responsive Design** - Works on desktop, tablet, and mobile
5. **Dark/Light Mode Ready** - Design system supports both themes

## API Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/api/getToken` | GET | Generate LiveKit access token |
| `/api/rooms` | GET | List available LiveKit rooms (via LiveKit API) |

## Technology Stack

- **Frontend**: React 18, Vite, CSS Modules
- **Backend**: Python Flask, LiveKit API
- **Styling**: Custom CSS with design system
- **Build Tool**: Vite (development), Webpack (not used)
- **Linting**: ESLint + React Hooks rules

## Deployment

1. Set up environment variables in `.env` (copy from `.env.example`)
2. Run backend: `python backend/server.py`
3. Run frontend: `cd frontend && npm run dev`
4. Access at `http://localhost:5173` (frontend) and `http://localhost:5001` (backend)

## Security

- LiveKit tokens are generated server-side and never exposed to client
- API keys are loaded from environment variables
- CORS configured to allow all origins (development only)
