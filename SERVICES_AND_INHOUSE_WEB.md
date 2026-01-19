# Services and In-House Web Service

This document describes how **MelakaTour** meets the following:

- **a.** Mobile application (Android/iOS)
- **b.** Integration with at least **THREE (3) third-party cloud computing services** (Google/Amazon/etc.)
- **c.** Integration with at least **ONE (1) in-house web service**
- **4.** Cloud services used (what and why)

---

## a. Mobile Application (Android/iOS)

**MelakaTour** is a **mobile application** built with **Flutter**, targeting **Android** (primary) . It runs on smartphones and tablets. The app is distributed as an APK on Android.

---

## b. Integration with at least THREE (3) Third-Party Cloud Computing Services

The app integrates with **four** third-party cloud computing services (all from **Google**), satisfying the requirement of at least three:

| # | Third-party cloud service | Provider | What we use it for |
|---|---------------------------|----------|---------------------|
| 1 | **Firebase Authentication** | Google | Sign up, login, logout, password reset, and auth state. |
| 2 | **Cloud Firestore** | Google | Database for `users` (profile, photo), `saved_places`, and planner/itinerary events. |
| 3 | **Firebase Cloud Functions** | Google | Serverless backend platform where we deploy and run our **in-house web service** (callable HTTP endpoints). |
| 4 | **Google Maps Platform** | Google | **Maps SDK for Android** (map in Navigate), **Places API** (Text Search, Nearby, Geocoding, Place Details, Autocomplete), and **Google Maps Directions URL** (“Open in Google Maps” from Explore and Planner). |

**Note:** Our in-house web service, when running on Firebase Cloud Functions, calls **Open-Meteo** (Forecast and Geocoding APIs) on the server. The **app** does not integrate with Open-Meteo directly; only our backend does.

---

## c. Integration with at least ONE (1) In-House Web Service

We integrate with **one in-house web service**, implemented as **two HTTP callable endpoints** that we develop and deploy to **Firebase Cloud Functions** (`functions/index.js`). The app calls them via `FirebaseFunctions.httpsCallable()`.

---

### 1. `getMelakaTourTips`

| | |
|-|-|
| **Input** | None |
| **Output** | `{ greeting: string, tip: string, timestamp: string (ISO) }` |
| **What it does** | Uses the server clock in **Asia/Kuala_Lumpur (GMT+8)** to pick a time-based greeting (Good Morning / Afternoon / Evening / Night) and **one random Melaka travel tip** from the matching list (e.g. A Famosa, cendol at Jonker Street, Melaka River Cruise). |
| **Used in app** | Explore screen (Dynamic Melaka Greeting card) |
| **APIs it uses** | None (only server time and our own logic) |

---

### 2. `analyzeMelakaWeather`

| | |
|-|-|
| **Input** | `{ lat: number, lon: number }` (from the app via device GPS) |
| **Output** | `{ suitabilityScore, recommendation, temperature, description, iconCode, locationName, forecast[] }` — current weather, 5-day forecast, 0–100 outdoor suitability score, and a text recommendation. |
| **What it does** | Fetches **weather** (Open-Meteo Forecast) and **place name** (Open-Meteo Reverse Geocoding) for the given coordinates; computes suitability from temperature and rain; maps WMO codes to descriptions and icons; returns data in the shape the app’s Weather model expects. |
| **Used in app** | Weather screen (Outdoor Suitability card, current weather, 5-day forecast) |
| **APIs it uses** | **Open-Meteo Forecast API**, **Open-Meteo Geocoding (Reverse) API** — both called **on the server only**; the app does not call them. |

---

## 4. Cloud Services Used (What and Why)

| Cloud service / API | What we use it for | Why we chose it |
|---------------------|--------------------|-----------------|
| **Firebase Authentication** | User sign up, login, logout, password reset, and session | No need for our own auth server; works with Firestore and mobile; fully managed by Google. |
| **Cloud Firestore** | `users`, `saved_places`, planner/itinerary (profiles, saved places, events) | NoSQL, real-time, per-user data; fits mobile; we avoid running and scaling our own database. |
| **Firebase Cloud Functions** | Runs our in-house callable logic (`getMelakaTourTips`, `analyzeMelakaWeather`) | Serverless: we only write logic, no servers to manage; integrates with Firebase; we can call Open-Meteo and other APIs on the server and keep API keys out of the app. |
| **Google Maps Platform** (Maps SDK, Places API, Geocoding, Directions URL) | Map display, place search, nearby, autocomplete, place details, photos, “Open in Google Maps” | Standard for maps and POI; required for a location- and travel-focused app. |
| **Open-Meteo** (Forecast, Reverse Geocoding) | Used **only by** `analyzeMelakaWeather` on the server for weather and place names | Free, no API key; sufficient for current weather, 5-day forecast, and reverse geocoding; we call it from our backend so the app stays simple and we can change weather provider later without an app update. |
