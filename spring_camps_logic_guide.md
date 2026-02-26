# 🏕️ Spring Break Camps: Logic & Data Mapping Guide

This guide explains how data moves from your Google Sheet into the Spring Break Camps page (`index.html`) and how the filters, maps, and UI elements work together.

---

## 1. 📍 Select Location (Main Area Filter)
**Source:** Database (Google Sheet)
**Logic:**
- The location dropdown (`filterLoc`) lists high-level "Areas" (e.g., KATY, WOODLANDS).
- **Mapping:** It pulls directly from **Column V (`Location Picker`)**.
  - *Fallback:* If Column V is empty, the code automatically builds it by combining **Column C (`Area`)** and **Column O (`Location`)** like this: `[Area] - [Location]`.
- The code splits the `Location Picker` text by the hyphen (`-`). 
  - The part *before* the hyphen becomes the **Main Area** (e.g., "KATY").
- The dropdown automatically populates with all unique Main Areas found in the sheet.

---

## 2. 📅 Week (Schedule Filter)
**Source:** Database (Google Sheet)
**Logic:**
- The week dropdown (`filterWeek`) shows the available weeks based on the Area selected.
- **Mapping:** It uses **Column P (`Week #`)** as the underlying ID (e.g., "1", "2").
- To show user-friendly text (like "Mar 9-11"), it looks at **Column R (`Tagline (100)`)** (or `Tagline`). It extracts the date text *before the comma*. 
- *Smart sorting:* If there are multiple sessions in the same week (e.g., a 3-day and a 5-day camp), the code automatically uses the longest date range for the label (e.g., it will prefer "Mar 9-13" over "Mar 9-11").

---

## 3. ⛺ Camps (The Main List)
**Source:** Hardcoded in HTML (`CAMPS` array) + Filtered by Database
**Logic:**
- The overarching camp categories (e.g., "Intro to Robotics...", "Advanced Game Dev...") are **hardcoded** in the `CAMPS` array in the javascript (around line 5500).
- **However, they are dynamically filtered!** 
- The system takes your active filters (Location, Week, Age, Experience, Interest) and checks them against the `allServices` loaded from Google Sheets.
- **The Link:** It uses the `matchNames` property. For example, if the hardcoded camp has `matchNames: ['Game Dev']`, it looks for services in the Google Sheet where **Column `Type of Camp Name`** equals "Game Dev". 
- If no services in the sheet match the current filters for that camp, the camp card is hidden. This is why it only shows "available camps after the data comes".

---

## 4. 🏷️ Location Tags on Camp Cards
**Source:** Dynamically generated from the Database
**Logic:**
- Underneath each camp title, you see orange tags like `📍 KATY` or `📍 WOODLANDS`.
- **How it works:** 
  1. The code looks at all available sessions in the Google Sheet for that specific camp type.
  2. It looks at **Column V (`Location Picker`)**.
  3. It extracts the **Sub-Location** (the part *after* the hyphen `-`). 
  4. It loops through all matching sessions and creates a tag for every unique sub-location it finds.
- **Why it changes when you select a Main Area:** If you select "KATY" from the top dropdown, the code filters the list of sessions *first*. Now, it only sees Katy sessions. So, when it generates the tags, it only generates tags for the Katy sub-locations. The "Woodlands" tag disappears because those sessions were filtered out.

---

## 5. ℹ️ Details Button
**Source:** Hardcoded
**Logic:**
- When a user clicks "Details", a modal opens with a long description.
- **Mapping:** This text is completely hardcoded in the `CAMPS` array under the `description` property (around line 5506 for the first camp).
- When clicked, the function `openDescription()` finds the matching camp by its `title` and displays the hardcoded text.

---

## 6. 🏢 Select Location (Sub-Location / Specific Building)
**Source:** Database (Google Sheet)
**Logic:**
- When you pick a Main Area (like "KATY") from the top dropdown, a dark blue box appears showing specific buildings (e.g., "Katy Cinco Ranch Lakehouse").
- **Mapping:** This comes from **Column V (`Location Picker`)**.
  - *Example:* `KATY - Katy Cinco Ranch Lakehouse`
  - The code splits this by the hyphen (`-`). 
  - The part *after* the hyphen becomes the **Sub-Location**.
- **The Address:** The smaller grey text under the sub-location title (e.g., "25202 Springwood Lake Dr...") comes directly from **Column O (`Location`)**.
- **Filtering:** Clicking one of these sub-location cards filters the camp grid to *only* show camps happening at that exact building.

---

## 7. 🗺️ Map Logic Coordinates
**Source:** Database (Google Sheet)
**Logic:**
- The interactive Leaflet map drops orange pins for the active sub-locations.
- **Mapping:** It pulls from **Column AE (`Map Link`)**.
- **How it gets coordinates:** The code looks at the Google Maps URL in Column AE and uses a "regular expression" to search for the `@lat,lng` string (e.g., `@30.1379956,-95.5536969`).
- It extracts those two numbers (Latitude and Longitude) and tells the map (`L.circleMarker`) to draw a pin there.
- *Fallback safety:* If Column AE is empty or missing coordinates, the code has a set of hardcoded default centers (like `[29.7858, -95.8245]` for Katy) and adds a tiny random offset so pins don't overlap perfectly.

---

## 8. 🎟️ Camp Options (The expanding dark box)
**Source:** Database (Google Sheet)
**Logic:**
- When you click "Select" on a camp, the row expands to show the specific sessions (e.g., "Morning - 3 Day", "Full Day - 5 Day").
- **How it populates:** It calls `renderInlineServices()`. It filters the Google Sheet data to find all rows where `Type of Camp Name` matches the camp you clicked, *and* matches your chosen Location/Week.
- **Mappings:**
  - **Service Title:** Comes from `Web Display Service name`, `Name (40)`, or `Name` (whichever it finds first).
  - **Schedule Text:** Comes from **Column R (`Tagline (100)`)** or `Tagline` (e.g., "Mar 9-11, Timings: 8:30 am to 11:30 am").
  - **Price:** Comes from **Column `Amount`** (e.g., `$275`).
  - **Spots Left:** A calculation based on internal capacity rules. If full, it shows "Fully Booked".
  - **Book Now Link:** The URL attached to the button comes directly from **Column `Direct Web Link`**.
- **Sorting:** The code forces a specific order: `Morning - 3 Day` -> `Afternoon - 3 Day` -> `Full` -> `5 Day` variations.

---
*Happy Coding! Let me know if you need to trace any other data points!*
