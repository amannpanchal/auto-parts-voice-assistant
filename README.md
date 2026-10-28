# LiveKit AI Car Call Centre

A modern car parts support platform featuring real-time voice assistance powered by LiveKit, with a React frontend and Python Flask backend.

## Overview

This application provides an interactive support experience for car parts customers, enabling them to connect with AI-powered voice assistants for help with parts lookup, compatibility checking, and ordering. The system integrates LiveKit's real-time audio/video streaming with a custom car parts database.

## Problem Statement

Car parts retailers need a way to provide expert assistance to customers at scale. Traditional phone support is expensive and doesn't scale well. This platform solves this by:

- **Real-time Voice Support**: Customers connect instantly with AI voice assistants via WebRTC
- **Car Parts Intelligence**: AI assistants can look up parts by VIN, check compatibility, and provide recommendations
- **Seamless Escalation**: Complex issues can be transferred to human agents
- **Modern UX**: Clean, responsive interface that works on all devices

## Key Features

- 🎤 **Live Voice Assistant** - Real-time audio streaming with LiveKit WebRTC
- 🚗 **Car Parts Lookup** - VIN-based vehicle identification and parts matching
- 💬 **Support Chat Modal** - Easy access to live support from any page
- 🌙 **Dark/Light Mode** - Automatic theme detection with manual toggle
- 📱 **Fully Responsive** - Works on mobile, tablet, and desktop
- ♿ **Accessible** - WCAG AA compliant with keyboard navigation
- 🔒 **Secure** - Server-side token generation, no exposed credentials

## Screenshots

> **Note**: Screenshots will be added after the UI redesign is complete.

| Page | Desktop | Mobile |
|------|---------|--------|
| Landing Page | ![Landing Desktop](docs/screenshots/landing-desktop.png) | ![Landing Mobile](docs/screenshots/landing-mobile.png) |
| Support Modal | ![Modal Desktop](docs/screenshots/modal-desktop.png) | ![Modal Mobile](docs/screenshots/modal-mobile.png) |
| Voice Assistant | ![Voice Desktop](docs/screenshots/voice-desktop.png) | ![Voice Mobile](docs/screenshots/voice-mobile.png) |

## Tech Stack

### Frontend
- **React 18.3** - UI framework
- **Vite 6.0** - Build tool and dev server
- **CSS Custom Properties** - Design system with theming
- **@livekit/components-react** - LiveKit React components
- **livekit-client** - LiveKit WebRTC client
- **ESLint** - Code linting

### Backend
- **Python 3.9+** - Runtime
- **Flask** - Web framework
- **LiveKit API** - Token generation and room management
- **livekit-agents** - AI agent framework
- **python-dotenv** - Environment variable management
- **Flask-CORS** - Cross-origin resource sharing

### Infrastructure
- **LiveKit Server** - WebRTC media server (cloud or self-hosted)
- **Database** - Car parts data (via `DatabaseDriver` abstraction)

## Project Architecture

```
LiveKit-AI-Car-Call-Centre-main/
├── backend/                    # Python Flask backend
│   ├── server.py              # Main Flask app + API routes
│   ├── api.py                 # LiveKit API integration
│   ├── db_driver.py           # Database abstraction
│   ├── prompts.py             # AI agent prompts
│   ├── requirements.txt       # Python dependencies
│   └── sample.env             # Environment template
├── frontend/                   # React frontend
│   ├── src/
│   │   ├── App.jsx            # Main app component
│   │   ├── App.css            # App-specific styles
│   │   ├── index.css          # Global styles + design system
│   │   ├── main.jsx           # Entry point
│   │   └── components/
│   │       ├── LiveKitModal.jsx       # Support modal
│   │       ├── SimpleVoiceAssistant.jsx # Voice UI
│   │       └── *.css                  # Component styles
│   ├── index.html             # HTML template
│   ├── package.json           # Dependencies
│   ├── vite.config.js         # Vite config
│   └── eslint.config.js       # ESLint config
├── docs/                       # Documentation
│   ├── ARCHITECTURE.md        # System architecture
│   ├── DEVELOPMENT.md         # Development guide
│   ├── API.md                 # API documentation
│   └── CHANGELOG.md           # Change history
├── .env.example               # Environment variables template
├── CONTRIBUTING.md            # Contribution guidelines
└── README.md                  # This file
```

## Prerequisites

- **Node.js** 18+
- **npm** 9+
- **Python** 3.9+
- **LiveKit Server** (cloud at [livekit.io](https://livekit.io) or self-hosted)

## Installation & Setup

### 1. Clone the Repository

```bash
git clone <repository-url>
cd LiveKit-AI-Car-Call-Centre-main
```

### 2. Configure Environment Variables

```bash
# Copy the template
cp .env.example .env

# Edit with your credentials
# Required: LIVEKIT_API_KEY, LIVEKIT_API_SECRET, VITE_LIVEKIT_URL
```

### 3. Backend Setup

```bash
cd backend
python -m venv venv
# Windows:
venv\Scripts\activate
# macOS/Linux:
source venv/bin/activate

pip install -r requirements.txt
python server.py
```

Backend runs on **http://localhost:5001**

### 4. Frontend Setup

```bash
cd frontend
npm install
npm run dev
```

Frontend runs on **http://localhost:5173** (proxies `/api/*` to backend)

## Environment Variables

| Variable | Description | Required |
|----------|-------------|----------|
| `LIVEKIT_API_KEY` | LiveKit API key from dashboard | Yes |
| `LIVEKIT_API_SECRET` | LiveKit API secret from dashboard | Yes |
| `VITE_LIVEKIT_URL` | LiveKit WebSocket URL (e.g., `wss://your-server.livekit.cloud`) | Yes |
| `FLASK_ENV` | Flask environment (`development` or `production`) | No |

See `.env.example` for a complete template.

## Running the Application

### Development Mode

```bash
# Terminal 1 - Backend
cd backend && python server.py

# Terminal 2 - Frontend
cd frontend && npm run dev
```

Open **http://localhost:5173** in your browser.

### Production Build

```bash
# Frontend
cd frontend && npm run build
# Output in frontend/dist/

# Backend (with gunicorn)
cd backend && gunicorn -w 4 -b 0.0.0.0:5001 server:app
```

## API Integration

### Get LiveKit Token

```bash
curl "http://localhost:5001/api/getToken?name=John%20Doe"
# Returns: JWT token string
```

### Frontend Usage

```javascript
// In LiveKitModal.jsx
const response = await fetch(`/api/getToken?name=${encodeURIComponent(userName)}`);
const token = await response.text();

// Use with LiveKitRoom
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

See [API.md](docs/API.md) for complete API documentation.

## Testing & Quality

### Frontend

```bash
cd frontend
npm run lint      # ESLint check
npm run build     # Type-check + production build
npm run preview   # Preview production build
```

### Backend

```bash
cd backend
# Add tests when available
python -m pytest
```

## Deployment

### Docker (Recommended)

```dockerfile
# Frontend Dockerfile
FROM node:18-alpine AS builder
WORKDIR /app
COPY frontend/package*.json ./
RUN npm ci
COPY frontend/ ./
RUN npm run build

FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Backend Dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY backend/requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY backend/ .
CMD ["gunicorn", "-w", "4", "-b", "0.0.0.0:5001", "server:app"]
```

### Manual Deployment

1. Build frontend: `npm run build`
2. Configure reverse proxy (nginx) to serve frontend and proxy `/api` to backend
3. Set production environment variables
4. Run backend with gunicorn or uvicorn
5. Configure SSL/TLS certificates

## Troubleshooting

### Common Issues

| Issue | Solution |
|-------|----------|
| `VITE_LIVEKIT_URL not defined` | Ensure `.env` exists in frontend/ with correct value |
| CORS errors | Backend allows all origins in dev; configure for production |
| LiveKit connection fails | Verify API key/secret and server URL; check firewall |
| Port 5173/5001 in use | Change ports in `vite.config.js` or `server.py` |
| Token expires quickly | Tokens valid for 1 hour; re-fetch on reconnect |

### Debug Mode

```bash
# Backend debug
FLASK_ENV=development python server.py

# Frontend debug
# Open browser DevTools → Console/Network tabs
# React DevTools for component inspection
```

## Future Improvements

- [ ] Add car parts catalog browsing
- [ ] Implement shopping cart and checkout
- [ ] Add human agent escalation queue
- [ ] Real-time transcription and translation
- [ ] Analytics dashboard for support metrics
- [ ] Multi-language support
- [ ] Mobile app (React Native)
- [ ] Integration with e-commerce platforms

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on:
- Code style and conventions
- Pull request process
- Testing requirements
- Design system usage

## License

Currently unlicensed. See [CONTRIBUTING.md](CONTRIBUTING.md) for contribution licensing terms.

## Support

- **Documentation**: [docs/](docs/)
- **Issues**: GitHub Issues
- **LiveKit Docs**: [docs.livekit.io](https://docs.livekit.io)

---

Built with ❤️ using React, LiveKit, and Flask