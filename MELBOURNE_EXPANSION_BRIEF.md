# DOORA Vision Assistant — Melbourne Expansion Brief

## Overview
This document outlines everything needed to add Melbourne as a second city to the existing DOORA Vision Assistant map tool. The goal is a single app with a city switcher so operators can serve renters across both Sydney and Melbourne from one URL.

---

## What Already Exists (Sydney)
- **216 clusters** across 9 zones
- **592 suburb boundaries** from ABS 2021 SAL data
- **Live site:** https://doorahelper.github.io/doora-map/
- **Repo:** https://github.com/doorahelper/doora-map

---

## What You Need to Prepare for Melbourne

### 1. Melbourne Cluster Data (REQUIRED)
Create a spreadsheet or JSON file with the same structure as Sydney. Each cluster needs:

| Field | Description | Example |
|-------|-------------|---------|
| **cc** | Unique cluster code | `MC1`, `MC2`, `SE1`, `NW3` |
| **name** | Descriptive cluster name | `Fitzroy Village`, `St Kilda Beachside` |
| **suburbs** | List of suburb names in the cluster | `["Fitzroy", "Collingwood", "Abbotsford"]` |
| **corridors** | Transport & access description (free text) | `"Anchored by Collingwood Station on the South Morang Line..."` |
| **alias** | Alternative name (optional) | `"Smith Street Precinct"` |
| **connects** | Array of related cluster codes | `["MC2", "MC5", "IC3"]` |
| **zone** | Zone grouping name | `"Inner Melbourne"` |

**Tips for building Melbourne clusters:**
- Group suburbs that share transport corridors, similar rent profiles, and lifestyle
- 3-6 suburbs per cluster works best (same as Sydney)
- Aim for 150-250 clusters to cover Greater Melbourne
- Zone names should reflect how Melburnians think about geography (e.g. "Inner North", "Western Suburbs", "Bayside", "South East", "Eastern Suburbs", "Northern Suburbs", "Mornington Peninsula")

### 2. Melbourne Zone Definitions (REQUIRED)
Define your zones and pick a colour for each. Sydney uses 9 zones — Melbourne might need 8-12 depending on how you slice it.

**Suggested Melbourne zones (adjust as needed):**

| Zone | Suggested Colour | Areas Covered |
|------|-----------------|---------------|
| Inner Melbourne | `#FF6B6B` (red) | CBD, Southbank, Docklands |
| Inner North | `#45B7D1` (blue) | Fitzroy, Carlton, Brunswick, Northcote |
| Inner East | `#FF8E72` (orange) | Richmond, Hawthorn, Kew |
| Inner West | `#4ECDC4` (teal) | Footscray, Yarraville, Seddon |
| Northern Suburbs | `#2ECC71` (green) | Reservoir, Preston, Bundoora |
| Eastern Suburbs | `#9B59B6` (purple) | Box Hill, Doncaster, Ringwood |
| South East | `#F39C12` (amber) | Dandenong, Springvale, Noble Park |
| Bayside | `#3498DB` (sky blue) | St Kilda, Brighton, Sandringham |
| Western Suburbs | `#E74C3C` (crimson) | Sunshine, Werribee, Melton |
| Mornington Peninsula | `#1ABC9C` (emerald) | Frankston, Mornington |

### 3. File Format
The easiest format is a JSON file matching the Sydney structure. Name it:
```
doora_melb_clusters.json
```

**Example entry:**
```json
{
  "cc": "IN1",
  "name": "Fitzroy Village",
  "suburbs": ["Fitzroy", "Collingwood"],
  "corridors": "Anchored by Collingwood Station on the South Morang Line. Smith Street and Brunswick Street provide the main commercial and nightlife corridors. Tram routes 86 and 11 connect to CBD in under 10 minutes.",
  "alias": "Smith Street Precinct",
  "connects": ["IN2", "IN5", "IM1"],
  "zone": "Inner North"
}
```

---

## What Claude Will Handle Automatically

When you're ready, start a new Claude Code session and say:
> "Read the doora-map repo and MELBOURNE_EXPANSION_BRIEF.md — let's add Melbourne"

Claude will then:

### 1. Download Melbourne Suburb Boundaries
- Source: ABS ArcGIS REST API (same as Sydney)
- Filter to Greater Melbourne bounding box
- Output: `melbourne_suburbs.geojson`
- Expected: ~500-700 suburb polygons

### 2. Add City Switcher UI
- Dropdown at the top of the sidebar: **Sydney | Melbourne**
- Switching cities will:
  - Load the correct cluster JSON
  - Load the correct suburb GeoJSON
  - Recenter the map (Sydney: -33.86, 151.21 | Melbourne: -37.81, 144.96)
  - Rebuild the sidebar buttons and zone groups
  - Clear any selected cluster

### 3. Refactor Code for Multi-City
- Extract city-specific config (center coords, zoom, data files, zone colours)
- Make all functions city-agnostic (they already mostly are)
- URL parameter support: `?city=melbourne` for direct links

### 4. Update GitHub Pages
- Push changes to the repo
- Site auto-deploys — same URL, now with both cities

---

## Checklist Before Starting

- [ ] Melbourne cluster data file ready (`doora_melb_clusters.json`)
- [ ] Zone names and colours decided
- [ ] All suburb names match ABS 2021 SAL naming (check abs.gov.au if unsure)
- [ ] Each cluster has `connects` links to other Melbourne clusters
- [ ] Corridor descriptions written for each cluster

---

## Timeline Estimate
Once the Melbourne cluster data is ready, the technical implementation should take **one session** (~1-2 hours):
- Download Melbourne boundaries: 10 min
- Add city switcher + refactor: 30 min
- Test all clusters load correctly: 20 min
- Visual verification + fixes: 20 min
- Push to GitHub Pages: 5 min

---

## Notes
- The ABS 2021 SAL data covers all of Australia, so Melbourne boundaries use the same API — just a different bounding box
- Overpass API (amenities) works globally, so schools/parks/hospitals will auto-populate for Melbourne too
- The talking summary generator is transport-mode-aware (train, tram, bus, ferry) — Melbourne's tram network will be detected automatically
- If a suburb name exists in both Sydney and Melbourne (unlikely but possible), the bounding box filter handles deduplication

---

*Document prepared: February 2026*
*For use with: https://github.com/doorahelper/doora-map*
