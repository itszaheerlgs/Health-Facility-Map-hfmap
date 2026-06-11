# Health Facility Map — hfmap

A web app that lets citizens find the nearest DOH-registered health facilities (RHU, BHS, hospitals, dialysis centers) using their GPS location or by searching by name or barangay.

Built with **React + Vite** (frontend) and **PHP + MySQL on XAMPP** (backend).

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | React 18, Vite, Google Maps JS API |
| Backend | PHP 8, Apache (XAMPP) |
| Database | MySQL 8 via phpMyAdmin |
| Maps | Google Maps JavaScript API + Geocoding API + Directions API |
| Geolocation | Browser `navigator.geolocation` API |

---

## Folder Structure

```
health-facility-app/          ← your project folder (anywhere on PC)
├── index.html
├── package.json
├── vite.config.js            ← proxy: /api → localhost/hfmap/api
├── .env.local                ← VITE_GOOGLE_MAPS_API_KEY
└── src/
    ├── main.jsx
    ├── App.jsx
    ├── components/
    │   ├── MapView.jsx       ← Google Map container
    │   ├── GeoButton.jsx     ← "Use my location" trigger
    │   ├── FacilityCard.jsx
    │   ├── SearchBar.jsx
    │   ├── FilterPanel.jsx
    │   ├── FacilityModal.jsx
    │   └── NearbyBadge.jsx
    ├── pages/
    │   ├── HomePage.jsx
    │   ├── FacilityDetailPage.jsx
    │   └── NotFoundPage.jsx
    ├── hooks/
    │   ├── useGeolocation.js  ← gets user lat/lng from browser
    │   ├── useGoogleMaps.js   ← loads Maps SDK once
    │   ├── useFacilities.js
    │   ├── useNearby.js
    │   ├── useFilters.js
    │   └── useDebounce.js
    ├── services/
    │   ├── api.js             ← all fetch() calls to PHP
    │   ├── mapsLoader.js
    │   ├── markerService.js
    │   ├── geocodingService.js
    │   └── directionsService.js
    ├── utils/
    │   ├── distanceCalc.js
    │   ├── facilityTypes.js
    │   ├── serviceFlags.js
    │   └── formatters.js
    └── styles/
        ├── variables.css
        ├── map.css
        ├── cards.css
        └── filters.css

C:/xampp/htdocs/hfmap/        ← XAMPP backend folder
├── config/
│   └── db.php                ← mysqli connection
├── api/
│   ├── facilities.php        ← GET all facilities
│   ├── nearby.php            ← GET ?lat=&lng= sorted by distance
│   ├── search.php            ← GET ?q= by name or barangay
│   ├── facility.php          ← GET ?id= single facility + services
│   └── update_coords.php     ← POST update lat/lng
└── db/
    └── hfmap.sql             ← import once via phpMyAdmin
```

---

## System Flow

### 1. App loads

```
Browser opens localhost:5173
  → React app mounts (main.jsx → App.jsx)
  → mapsLoader.js injects Google Maps script tag using VITE_GOOGLE_MAPS_API_KEY
  → useGoogleMaps.js waits for window.google to be ready
  → MapView.jsx renders the Google Map centered on Philippines (14.5995, 120.9842)
  → useFacilities.js fires: GET /api/facilities.php
  → facilities.php queries MySQL → returns JSON array
  → markerService.js places a marker on the map for each facility
```

### 2. User clicks "Use my location"

```
GeoButton.jsx clicked
  → useGeolocation.js calls navigator.geolocation.getCurrentPosition()
  → Browser asks user for location permission
  → On allow: returns { lat, lng }
  → Map pans and zooms to user's coordinates
  → useNearby.js fires: GET /api/nearby.php?lat=14.6760&lng=121.0320&radius=5
  → nearby.php runs Haversine SQL query → returns facilities sorted by distance
  → Sidebar updates with nearest facilities listed first
  → NearbyBadge.jsx shows "X km away" on each card
```

### 3. User searches by name or barangay

```
SearchBar.jsx — user types (debounced 300ms via useDebounce.js)
  → api.js fires: GET /api/search.php?q=payatas
  → search.php runs LIKE query on name, barangay, municipality
  → Returns matching facilities as JSON
  → Map markers filter to show only results
  → Sidebar shows matched cards
```

### 4. User filters by facility type or service

```
FilterPanel.jsx — user checks RHU / BEmONC / 24hrs / DC
  → useFilters.js applies filter to current facility list (client-side)
  → No extra API call — filtering happens on already-fetched data
  → Map markers and sidebar cards update instantly
```

### 5. User clicks a facility card or marker

```
FacilityCard.jsx or map marker clicked
  → api.js fires: GET /api/facility.php?id=3
  → facility.php queries facilities JOIN facility_services
  → Returns facility detail + services array as JSON
  → FacilityModal.jsx opens showing:
       name, type, address, contact, hours
       services list (BEmONC, TB-DOTS, etc.)
       "Get Directions" button
```

### 6. User clicks "Get Directions"

```
FacilityModal.jsx — "Get Directions" clicked
  → directionsService.js calls Google Maps Directions API
       origin: user's lat/lng (from useGeolocation)
       destination: facility lat/lng
       travelMode: DRIVING
  → Route drawn on map
  → Step-by-step directions shown in sidebar
```

---

## API Endpoints

All endpoints return `Content-Type: application/json`.

| Method | Endpoint | Params | Returns |
|---|---|---|---|
| GET | `/api/facilities.php` | — | All active facilities |
| GET | `/api/nearby.php` | `lat`, `lng`, `radius` (km, default 5) | Facilities sorted by distance |
| GET | `/api/search.php` | `q` (search term) | Matching facilities |
| GET | `/api/facility.php` | `id` | Single facility + services |
| POST | `/api/update_coords.php` | `id`, `lat`, `lng` | Updated record |

### Example response — `/api/nearby.php?lat=14.676&lng=121.032&radius=5`

```json
[
  {
    "id": 6,
    "name": "Project 6 Health Center",
    "type": "RHU",
    "address": "Project 6, Quezon City",
    "barangay": "Project 6",
    "municipality": "Quezon City",
    "lat": "14.6560000",
    "lng": "121.0070000",
    "contact": "02-3721100",
    "hours": "Mon-Fri 8AM-5PM",
    "is_24hrs": 0,
    "distance_km": 1.24,
    "services": ["TB-DOTS", "Vaccination Services"]
  }
]
```

---

## Database Schema

### `facilities`

| Column | Type | Notes |
|---|---|---|
| id | INT PK | auto increment |
| name | VARCHAR(255) | facility name |
| type | VARCHAR(100) | RHU, BHS, Hospital, DC, Clinic |
| address | VARCHAR(500) | full address |
| barangay | VARCHAR(150) | |
| municipality | VARCHAR(150) | |
| province | VARCHAR(150) | |
| region | VARCHAR(100) | |
| lat | DECIMAL(10,7) | latitude |
| lng | DECIMAL(10,7) | longitude |
| contact | VARCHAR(100) | phone number |
| email | VARCHAR(150) | |
| hours | VARCHAR(200) | operating hours text |
| is_24hrs | TINYINT(1) | 1 = open 24 hours |
| is_active | TINYINT(1) | 1 = show on map |
| created_at | TIMESTAMP | |
| updated_at | TIMESTAMP | auto-updates |

### `facility_services`

| Column | Type | Notes |
|---|---|---|
| id | INT PK | |
| facility_id | INT FK | references facilities.id |
| service_code | VARCHAR(50) | BEMONC, CEMONC, TBDOTS, DC |
| service_label | VARCHAR(150) | display name |

---

## Local Setup

### Requirements

- XAMPP (Apache + MySQL running)
- Node.js 18+
- Google Maps API key (Maps JS, Geocoding, Directions APIs enabled)

### Steps

**1. Import the database**
```
Open localhost/phpmyadmin
Click Import → choose db/hfmap.sql → Go
```

**2. Copy backend to XAMPP**
```
Copy the htdocs/hfmap/ folder into C:/xampp/htdocs/
```

**3. Set up the frontend**
```bash
cd health-facility-app
npm install
```

Create `.env.local`:
```
VITE_GOOGLE_MAPS_API_KEY=your_api_key_here
VITE_API_BASE=http://localhost/hfmap/api
```

**4. Run**
```bash
# Make sure XAMPP Apache + MySQL are ON, then:
npm run dev
```

Open `localhost:5173`

---

## Environment Variables

| Variable | Where | Value |
|---|---|---|
| `VITE_GOOGLE_MAPS_API_KEY` | `.env.local` | Your Google Maps API key |
| `VITE_API_BASE` | `.env.local` | `http://localhost/hfmap/api` |
| DB host/user/pass | `config/db.php` | XAMPP defaults: `localhost`, `root`, `` |

---

## Expected Results

| Feature | Expected behaviour |
|---|---|
| Map loads | Philippines map centered, all facility markers visible |
| Geolocation | Blue dot appears at user location, nearby list updates |
| Search | Results filter in real-time as user types |
| Filters | RHU/BHS/Hospital/DC checkboxes narrow markers instantly |
| Facility click | Modal opens with full details and services |
| Get directions | Route drawn on map from user to facility |
| 24hrs filter | Only shows facilities with `is_24hrs = 1` |
| BEmONC filter | Only shows facilities with BEMONC in facility_services |

---

## Notes

- The Haversine distance calculation runs in SQL inside `nearby.php` for performance.
- All PHP endpoints set `Access-Control-Allow-Origin: *` headers to allow Vite dev server requests.
- The Google Maps SDK is loaded once via `mapsLoader.js` and shared across all components.
- `vite.config.js` proxies `/api` to `http://localhost/hfmap/api` so you never hardcode the backend URL in components — only `api.js` knows about it.
