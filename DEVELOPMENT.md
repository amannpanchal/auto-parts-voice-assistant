# Development Guide

## Prerequisites

- **Node.js** 18+ and npm
- **Python** 3.9+
- **LiveKit Server** (cloud or self-hosted)

## Setup

### Backend

```bash
cd backend
python -m venv venv
source venv/bin/activate  # or venv\Scripts\activate on Windows
pip install -r requirements.txt
cp ../.env.example .env
# Edit .env with your LiveKit credentials
python server.py
```

The backend will start on `http://localhost:5001`

### Frontend

```bash
cd frontend
npm install
cp ../.env.example .env
# Edit .env with VITE_LIVEKIT_URL
npm run dev
```

The frontend will start on `http://localhost:5173`

## Development Workflow

1. **Start both servers** in separate terminals
2. **Frontend hot-reload**: Vite provides HMR out of the box
3. **Backend reload**: Flask debug mode auto-reloads on changes
4. **API proxy**: Vite proxies `/api/*` to `http://localhost:5001`

## Code Conventions

### Frontend (React)

- **Components**: PascalCase for components, camelCase for functions
- **Styles**: CSS Modules or CSS-in-JS (current: plain CSS with design system)
- **State**: React hooks (`useState`, `useEffect`, `useCallback`)
- **Imports**: Relative imports for local files, absolute for packages
- **ESLint**: Run `npm run lint` before committing

### Backend (Python)

- **Style**: PEP 8 with type hints where possible
- **Imports**: Group standard library, third-party, local
- **Async**: Use `async/await` for LiveKit API calls
- **Environment**: Load via `python-dotenv`

## Design System

The project uses a custom design system defined in `frontend/src/index.css` and `frontend/src/App.css`:

### Color Palette
- **Primary**: Indigo (`#4F46E5`)
- **Secondary**: Blue (`#3B82F6`)
- **Background**: Light (`#F8FAFC`) / Dark (`#0F172A`)
- **Surface**: White / Dark (`#1E293B`)
- **Text**: Dark (`#1E293B`) / Light (`#F1F5F9`)
- **Accent**: Emerald (`#10B981`) / Amber (`#F59E0B`)

### Spacing
- Base unit: 4px
- Scale: 4px, 8px, 16px, 24px, 32px, 48px

### Typography
- Font: Inter, system-ui, sans-serif
- Scale: xs (12px), sm (14px), base (16px), lg (18px), xl (20px), 2xl (24px), 3xl (30px), 4xl (36px)

## Testing

```bash
# Frontend
cd frontend
npm run lint      # ESLint check
npm run build     # Production build test

# Backend
cd backend
python -m pytest  # If tests exist (currently none)
```

## Building for Production

### Frontend
```bash
cd frontend
npm run build
# Output in frontend/dist/
```

### Backend
```bash
cd backend
# Use a production WSGI server like gunicorn
gunicorn -w 4 -b 0.0.0.0:5001 server:app
```

## Common Issues

### "VITE_LIVEKIT_URL not defined"
Make sure `.env` file exists in frontend directory with `VITE_LIVEKIT_URL=wss://your-server.com`

### "CORS error"
Backend has `CORS(app, resources={r"/*": {"origins": "*"}})` - ensure frontend origin is allowed

### "LiveKit connection failed"
Check that `LIVEKIT_API_KEY` and `LIVEKIT_API_SECRET` are valid and the server is reachable

### "Port already in use"
- Frontend: 5173 (Vite default)
- Backend: 5001 (Flask default)
- Change in `vite.config.js` or `server.py` if needed

## Debugging

### Frontend
- React DevTools for component inspection
- Vite dev server console for HMR issues
- Browser network tab for API calls

### Backend
- Flask debug mode prints requests to console
- LiveKit API logs via `logger` in `api.py`

## Extending the Project

### Adding New Components
1. Create component in `frontend/src/components/`
2. Add styles in same directory or use design system classes
3. Import and use in `App.jsx` or other components

### Adding API Endpoints
1. Add route in `backend/server.py`
2. Use Flask async for LiveKit operations
3. Test with `curl http://localhost:5001/api/your-endpoint`

### Styling New Components
- Use design system CSS variables from `index.css`
- Follow existing component patterns in `App.css`
- Add responsive breakpoints: 640px, 768px, 1024px, 1280px

## Version Control

```bash
# Recommended workflow
git checkout -b feature/your-feature
# Make changes
git add .
git commit -m "feat: descriptive message"
git push origin feature/your-feature
# Open PR
```

## Environment-Specific Configs

| Environment | Frontend URL | Backend URL | Notes |
|-------------|--------------|-------------|-------|
| Development | `http://localhost:5173` | `http://localhost:5001` | Vite proxy |
| Staging | `https://staging.example.com` | `https://api-staging.example.com` | Set in .env |
| Production | `https://example.com` | `https://api.example.com` | Use reverse proxy |

## Monitoring

- **Frontend**: Console logs, React error boundary
- **Backend**: Flask access logs, LiveKit server metrics
- **LiveKit**: Dashboard at your LiveKit server URL