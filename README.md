# Melody — Music Streaming Demo

A responsive React music-streaming demo with an Express/MongoDB API. It includes registration and sign-in, a searchable catalog, browser audio playback, favorites and listening history, playlists, song reviews and ratings, profile editing, and a protected admin catalog screen.

> **Audio and rights:** the seeded catalog uses SoundHelix example audio and Unsplash cover images for demonstration only. Check each provider's current terms before publishing. Use audio and artwork that you own or are explicitly licensed to distribute. The downloads page intentionally does not pretend that streaming URLs are downloadable.

## Requirements

- Node.js 18.11 or newer and npm
- MongoDB Community Server running locally, or a MongoDB Atlas connection string

## Run in VS Code

Open this project folder in VS Code. Open two integrated terminals, one for the backend and one for the frontend.

### 1. Configure MongoDB and the backend

In the first terminal:

```powershell
cd backend
npm install
Copy-Item .env.example .env
```

Open `backend/.env`. For a local MongoDB installation, the example URI works when MongoDB is running. For Atlas, replace `MONGODB_URI` with the connection string from your Atlas cluster. Set `JWT_SECRET` to a long, random secret (at least 24 characters), and leave `CLIENT_URL` as `http://localhost:5173` for local development.

Load the sample tracks and optionally provision an administrator account. Set `ADMIN_EMAIL` and `ADMIN_PASSWORD` in `backend/.env` before seeding if you want to sign in to the admin dashboard:

```powershell
npm run seed
npm run dev
```

The API runs at `http://localhost:5000`. Check `http://localhost:5000/api/health` to confirm the server and database are available. Do not use demo credentials or the sample JWT secret in a public deployment.

### 2. Run the React website

In a second VS Code terminal:

```powershell
cd frontend
npm install
Copy-Item .env.example .env
npm run dev
```

Open the local URL Vite prints (normally `http://localhost:5173`). `VITE_API_URL` in `frontend/.env` should point to the backend API.

## Using the demo

- Create an account with a valid email and a password of at least eight characters.
- Search by song, artist, album, or genre; use the genre filters to narrow results.
- Click a track's play button or use the persistent bottom player. Browser playback requires an internet connection to the sample audio host.
- Sign in to save favorites, maintain listening history, create playlists, and post one editable review per song.
- To manage catalog tracks, set `ADMIN_EMAIL` and `ADMIN_PASSWORD` in `backend/.env`, run `npm run seed`, then sign in using that administrator account. Admin API routes verify the account role on the server.

## API overview

| Method | Endpoint | Access | Purpose |
|---|---|---|---|
| POST | `/api/auth/register` | Public | Create an account |
| POST | `/api/auth/login` | Public | Sign in and receive a JWT |
| GET/PATCH | `/api/auth/me` | Signed in | Get or update profile |
| GET | `/api/songs?q=&genre=` | Public | Search/filter catalog |
| POST/DELETE | `/api/songs` and `/api/songs/:id` | Admin | Add/remove catalog tracks |
| GET/POST | `/api/auth/me/library`, `/favorites/:songId`, `/history/:songId` | Signed in | View/update library |
| GET/POST/DELETE | `/api/playlists` | Signed in | Create and manage playlists |
| GET/POST | `/api/reviews/:songId` | Public/read, signed-in/write | View and submit ratings |
| GET/DELETE | `/api/reviews` and `/api/reviews/:id` | Admin | Moderate reviews |

The UI includes a Downloads page, but actual offline storage is not offered by this demo because the sample streaming catalog does not grant download rights. Artists and albums are represented as song metadata rather than separate editable collections.

## Deployment

1. Provision MongoDB Atlas and configure network access for your hosting provider.
2. Deploy the backend to a Node-compatible service. Set `MONGODB_URI`, a strong unique `JWT_SECRET`, `CLIENT_URL` (your deployed frontend origin), and optional `ADMIN_EMAIL`/`ADMIN_PASSWORD`. Run `npm install` and `npm start`; seed the catalog with `npm run seed`.
3. Deploy `frontend` as a static Vite site. Set `VITE_API_URL` to the deployed backend's `/api` URL before building, then run `npm install` and `npm run build`; publish `dist`.
4. Configure the frontend host to serve `index.html` for app routes, and configure the API host's CORS origin to the exact frontend origin.
5. Replace demo media with properly licensed files on storage/CDN you control. Use HTTPS and rotate credentials before launch.
