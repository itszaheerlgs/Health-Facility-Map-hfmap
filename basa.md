# Health Facility Map — hfmap

A web app that lets citizens find the nearest DOH-registered health facilities (RHU, BHS, hospitals, dialysis centers) using their GPS location or by searching by name or barangay.

Built with **React + Vite** (frontend) and **PHP + MySQL on XAMPP** (backend).


## Folder Structure

```
health-facility-app/          ← your project folder (anywhere on PC)
├── index.html✅
├── package.json✅
├── vite.config.js✅            ← proxy: /api → localhost/hfmap/api
├── .env.local✅                ← VITE_GOOGLE_MAPS_API_KEY
└── src/
    ├── main.jsx✅
    ├── App.jsx✅
    ├── components/
    │   ├── MapView.jsx✅       ← Google Map container
    │   ├── GeoButton.jsx✅     ← "Use my location" trigger
    │   ├── FacilityCard.jsx✅
    │   ├── SearchBar.jsx✅
    │   ├── FilterPanel.jsx✅
    │   ├── FacilityModal.jsx✅
    │   └── NearbyBadge.jsx✅
    ├── pages/
    │   ├── HomePage.jsx
    │   ├── FacilityDetailPage.jsx
    │   └── NotFoundPage.jsx
    ├── hooks/
    │   ├── useGeolocation.js✅  ← gets user lat/lng from browser
    │   ├── useGoogleMaps.js✅   ← loads Maps SDK once
    │   ├── useFacilities.js✅
    │   ├── useNearby.js✅
    │   ├── useFilters.js✅
    │   └── useDebounce.js✅
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
│   └── db.php✅                ← mysqli connection
├── api/
│   ├── facilities.php✅        ← GET all facilities
│   ├── nearby.php✅            ← GET ?lat=&lng= sorted by distance
│   ├── search.php✅            ← GET ?q= by name or barangay
│   ├── facility.php✅          ← GET ?id= single facility + services
│   └── update_coords.php✅     ← POST update lat/lng
└── db/
    └── hfmap.sql✅             ← import once via phpMyAdmin

