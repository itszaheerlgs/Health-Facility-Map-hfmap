# ✚ HFMap — Health Facility Locator

> A free, open-source web app for finding DOH-registered health facilities in **Quezon City, Philippines**.  
> Built with React + Vite (frontend) and PHP + MySQL via XAMPP (backend).  
> Uses **Leaflet + OpenStreetMap** — no Google Maps API key, no billing, 100% free.

---

## 📸 Preview

> _Map view with sidebar, stats bar, search, filters, and facility details._

![HFMap Preview](./preview.png)

---

## ✨ Features

### 🗺 Map
- Interactive map powered by **Leaflet + OpenStreetMap** (free, no API key)
- Color-coded markers per facility type
- Pan to user location with a blue GPS dot
- Click markers to view facility details

### 🔍 Search & Filter
- Search by facility name, barangay, or municipality
- Filter by facility type (Hospital, RHU, BHS, Health Center, etc.)
- Filter by services (BEmONC, CEmONC, TB-DOTS, Dialysis, PhilHealth, OPD, Vaccination)
- Toggle **24-hour only** facilities
- Reset all filters in one click

### 📋 Facility Info
- Full detail modal with address, contact, email, hours, and services
- Color-coded type badge per facility
- PhilHealth and service tags
- **Get Directions** button (opens Google Maps navigation)
- View on Google Maps link

### 📍 Location
- **Use My Location** button (GPS geolocation)
- Nearby facilities sorted by distance (Haversine formula)
- Distance badge on each card (🟢 very close, 🔵 close)

### 🧭 Navigation Bar
- Collapsible sidebar with hamburger toggle
- Live facility count badge
- **ℹ About** modal with app info and how-to-use guide
- **🏥 DOH HFR** quick link to the official DOH Health Facility Registry

### 📊 Stats Bar
- Total facility count
- 24-hour facility count
- Breakdown by top facility types
- "Shown" count when filters are active

### 📱 Responsive
- Mobile-friendly layout
- Sidebar overlays map on small screens

---

## 🚀 Planned Features

- [ ] Marker clustering (group pins when zoomed out)
- [ ] Heatmap layer (facility density)
- [ ] Draw radius circle around user
- [ ] Satellite / terrain map layer toggle
- [ ] Dark mode map + UI
- [ ] Search autocomplete / suggestions dropdown
- [ ] Filter by distance radius (1km, 3km, 5km, 10km)
- [ ] Filter by "open now" based on current time
- [ ] Sort results by name / distance / type
- [ ] Save favorite / bookmarked facilities
- [ ] Recent searches history
- [ ] Photo gallery per facility
- [ ] Community ratings and reviews
- [ ] Report incorrect info button
- [ ] "Is this open right now?" live status
- [ ] Bed availability indicator (hospitals)
- [ ] DOH license number display
- [ ] QR code per facility for sharing
- [ ] In-app turn-by-turn directions panel
- [ ] Estimated travel time display
- [ ] "Route to nearest facility" one-tap button
- [ ] Share facility link (copy URL)
- [ ] Share via SMS / social media
- [ ] Printable facility info card
- [ ] Export list as PDF or Excel
- [ ] Admin dashboard page
- [ ] Facility count charts (bar / pie)
- [ ] Coverage map (barangay coverage view)
- [ ] Add / edit / delete facilities via admin form
- [ ] Bulk import from DOH CSV
- [ ] Auto-geocode missing coordinates
- [ ] Audit log (who changed what)
- [ ] Browser push notifications for nearby facilities
- [ ] Emergency mode (hospitals + 24hr only)
- [ ] PWA — install as mobile app
- [ ] Offline mode with cached data
- [ ] Language toggle (Filipino / English)
- [ ] Font size adjuster
- [ ] Full keyboard navigation support
- [ ] Screen reader / accessibility improvements

---

## 🛠 Tech Stack

| Layer      | Tech                          |
|------------|-------------------------------|
| Frontend   | React 18 + Vite               |
| Map        | Leaflet 1.9 + OpenStreetMap   |
| Geocoding  | Nominatim (OSM) — free        |
| Backend    | PHP 8 + MySQL                 |
| Local Dev  | XAMPP                         |
| Styling    | Plain CSS with design tokens  |
| Routing    | React Router v6               |

---

## 📁 Project Structure

```
hfmap/
├── api/                        # PHP REST API endpoints
│   ├── facilities.php          # GET all active facilities
│   ├── facility.php            # GET single facility by ID
│   ├── nearby.php              # GET facilities within radius (Haversine)
│   ├── search.php              # GET search by name/barangay/municipality
│   └── update_coords.php       # POST update lat/lng (admin)
│
├── config/
│   └── db.php                  # MySQL connection config
│
├── db/
│   └── hfmap.sql               # Database schema + seed data
│
└── health-facility-app/        # React + Vite frontend
    ├── src/
    │   ├── components/
    │   │   ├── Navbar.jsx
    │   │   ├── StatsBar.jsx
    │   │   ├── MapView.jsx
    │   │   ├── SearchBar.jsx
    │   │   ├── FilterPanel.jsx
    │   │   ├── FacilityCard.jsx
    │   │   ├── FacilityModal.jsx
    │   │   ├── AboutModal.jsx
    │   │   ├── GeoButton.jsx
    │   │   └── NearbyBadge.jsx
    │   ├── hooks/
    │   │   ├── useFacilities.js
    │   │   ├── useFilters.js
    │   │   ├── useNearby.js
    │   │   ├── useGeolocation.js
    │   │   ├── useGoogleMaps.js    # (Leaflet loader, kept name for compat)
    │   │   └── useDebounce.js
    │   ├── services/
    │   │   ├── api.js              # All fetch() calls to PHP backend
    │   │   ├── mapsLoader.js       # Leaflet CDN loader
    │   │   ├── markerService.js    # Create/clear Leaflet markers
    │   │   ├── geocodingService.js # Nominatim geocoding (free)
    │   │   └── directionsService.js# Google Maps directions URL builder
    │   ├── pages/
    │   │   ├── FacilityDetailPage.jsx
    │   │   ├── HomePage.jsx
    │   │   └── NotFoundPage.jsx
    │   ├── styles/
    │   │   ├── variables.css       # Design tokens
    │   │   ├── navbar.css
    │   │   ├── map.css
    │   │   ├── cards.css
    │   │   └── filters.css
    │   └── utils/
    │       ├── facilityTypes.js
    │       ├── serviceFlags.js
    │       ├── formatters.js
    │       └── distanceCalc.js
    ├── .env.local
    ├── vite.config.js
    └── package.json
```

---

## ⚙️ Setup & Installation

### Prerequisites
- [XAMPP](https://www.apachefriends.org/) (Apache + MySQL)
- [Node.js](https://nodejs.org/) v18+
- npm v9+

---

### 1. Clone the repo

```bash
git clone https://github.com/yourusername/hfmap.git
cd hfmap
```

---

### 2. Set up the database

1. Start **Apache** and **MySQL** in XAMPP Control Panel
2. Open **phpMyAdmin** → `http://localhost/phpmyadmin`
3. Create a new database named **`hfmap`**
4. Click on `hfmap` → **Import** tab → choose `db/hfmap.sql` → **Go**

---

### 3. Configure the backend

Copy the project folder into your XAMPP `htdocs`:

```
C:\xampp\htdocs\hfmap\
```

Check `config/db.php` — default credentials (XAMPP default):

```php
define('DB_HOST', 'localhost');
define('DB_USER', 'root');
define('DB_PASS', '');        // empty by default
define('DB_NAME', 'hfmap');
```

Change these if you set a custom MySQL password.

---

### 4. Set up the frontend

```bash
cd health-facility-app
npm install
```

Check `.env.local`:

```env
VITE_API_BASE=http://localhost/hfmap/api
```

> If your XAMPP htdocs folder is named differently, update `hfmap` in the path above.

---

### 5. Run the dev server

```bash
npm run dev
```

Open your browser at: **http://localhost:5173**

---

### 6. Build for production

```bash
npm run build
```

Output goes to `health-facility-app/dist/`. Deploy that folder to your web server.

---

## 🗄 Database Schema

### `facilities`

| Column       | Type            | Description                        |
|--------------|-----------------|------------------------------------|
| id           | INT (PK)        | Auto-increment                     |
| name         | VARCHAR(255)    | Facility name                      |
| type         | VARCHAR(100)    | RHU, BHS, Hospital, DC, Clinic...  |
| address      | VARCHAR(500)    | Full street address                |
| barangay     | VARCHAR(150)    | Barangay name                      |
| municipality | VARCHAR(150)    | City / municipality                |
| province     | VARCHAR(150)    | Province                           |
| region       | VARCHAR(100)    | Region (e.g. NCR)                  |
| lat          | DECIMAL(10,7)   | Latitude coordinate                |
| lng          | DECIMAL(10,7)   | Longitude coordinate               |
| contact      | VARCHAR(100)    | Phone number                       |
| email        | VARCHAR(150)    | Email address                      |
| hours        | VARCHAR(200)    | Operating hours string             |
| is_24hrs     | TINYINT(1)      | 1 = open 24 hours                  |
| is_active    | TINYINT(1)      | 1 = visible on map                 |
| created_at   | TIMESTAMP       | Record creation time               |
| updated_at   | TIMESTAMP       | Last update time                   |

### `facility_services`

| Column        | Type          | Description                              |
|---------------|---------------|------------------------------------------|
| id            | INT (PK)      | Auto-increment                           |
| facility_id   | INT (FK)      | References `facilities.id`               |
| service_code  | VARCHAR(50)   | BEMONC, CEMONC, TBDOTS, DC, VAX, etc.   |
| service_label | VARCHAR(150)  | Human-readable service name              |

---

## 🌐 API Endpoints

All endpoints return JSON.

| Method | Endpoint                        | Description                            |
|--------|---------------------------------|----------------------------------------|
| GET    | `/api/facilities.php`           | All active facilities                  |
| GET    | `/api/facility.php?id=1`        | Single facility by ID                  |
| GET    | `/api/nearby.php?lat=&lng=&radius=5` | Facilities within radius (km)     |
| GET    | `/api/search.php?q=payatas`     | Search by name / barangay / address    |
| POST   | `/api/update_coords.php`        | Update facility coordinates (admin)    |

---

## 📦 Data Source

Facility data is sourced from the **DOH Health Facility Registry (HFR)**:  
🔗 https://hfr.doh.gov.ph

The seed data in `hfmap.sql` includes 10 sample Quezon City facilities for development.  
To populate with real DOH data, export from HFR and import via the bulk import tool _(coming soon)_.

---

## 🤝 Contributing

1. Fork the repo
2. Create a feature branch: `git checkout -b feature/my-feature`
3. Commit your changes: `git commit -m "Add my feature"`
4. Push to the branch: `git push origin feature/my-feature`
5. Open a Pull Request

---

## 📄 License

MIT License — free to use, modify, and distribute.

---

## 👤 Author

Made with ❤️ for the Quezon City community.  
Data from the [Department of Health — Health Facility Registry](https://hfr.doh.gov.ph).

---

> _"Malapit na ang tulong."_ — Help is nearby.




❤️ User Personalization

 Save favorite / bookmarked facilities
 Recent searches history


🏥 Facility Info Upgrades

 Photo gallery per facility
 Community ratings and reviews
 Report incorrect info button
 "Is this open right now?" live status
 Bed availability indicator (hospitals)
 DOH license number display
 QR code per facility for sharing


🧭 Navigation & Directions

 In-app turn-by-turn directions panel
 Estimated travel time display
 "Route to nearest facility" one-tap button


📤 Sharing & Export

 Share facility link (copy URL)
 Share via SMS / social media
 Printable facility info card
 Export list as PDF or Excel


⚙️ Admin Panel

 Admin dashboard page
 Facility count charts (bar / pie)
 Coverage map (barangay coverage view)
 Add / edit / delete facilities via admin form
 Bulk import from DOH CSV
 Auto-geocode missing coordinates
 Audit log (who changed what)


🔔 Alerts & Emergency

 Browser push notifications for nearby facilities
 Emergency mode (hospitals + 24hr only)


📱 Mobile & PWA

 PWA — install as mobile app
 Offline mode with cached data


♿ Accessibility & Language

 Language toggle (Filipino / English)
 Font size adjuster
 Full keyboard navigation support
 Screen reader / accessibility improvements

