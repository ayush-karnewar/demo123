# HOTSPOT Changelog

> All changes to this project are documented here.
> **Checkpoint: v1.5.3** — Revert to this tag if anything breaks: `git checkout v1.5.3`

---

## v3.6.3 — Comprehensive README for GitHub
- Replaced Vite template README with a complete project documentation
- Includes: 30-second pitch, feature table by platform, architecture diagram, complete tech stack, full API reference, setup instructions, demo credentials, WhatsApp bot setup, key design decisions with expandable sections, performance metrics, roadmap, security notes
- Hero banner with badges (version, license, Python, React, FastAPI, NIRMAN, Problem #4)
- One-line pitch prominently displayed: "Detecting a pothole doesn't fix it. A watched government does."

## v3.6.2 — Wall of Shame: region-based contractor mapping
- Added 4 new mock contractors: South Mumbai Roadworks, Western Suburbs Builders, Eastern Suburbs Contractor, CIDCO Infrastructure
- Expanded area→contractor keyword mapping: 30+ Mumbai / Navi Mumbai location keywords (Marine Drive, Bandra, Andheri, Chembur, Vashi, Kharghar, Panvel, etc.)
- Wall of Shame now ranks 6 contractors (was 2) — realistic accountability showcase
- Each contractor gets ~3-12 reports spread across their zone, with varied fix rates and negligence scores

## v3.6.1 — Real AI-detected demo reports across Mumbai + Navi Mumbai
- Added backend/seed_from_images.py — reads images from any folder, runs full AI detection, generates N demo reports
- Replaced 8 hardcoded fake demo reports with 20 real image-based reports
- Each report has annotated_image from YOLO detection + real severity + real cost estimate
- Distributed across 20 landmarks: 10 Mumbai (Marine Drive, Sea Link, Dadar, Andheri, BKC, etc.) + 10 Navi Mumbai (Vashi, Nerul, Belapur, Kharghar, Panvel, etc.)
- Status mix: 9 submitted, 5 in_progress, 3 acknowledged, 3 fixed (for realistic dashboard variety)
- Reporter variety, realistic timestamps spread across 14 days, upvote counts scaled by severity
- Startup flow: loads from shared_store.json if present, else falls back to legacy 8-report seed

## v3.6.0 — Presentation rebuilt: 15-slide deck with all groundbreaking features
- Completely rewrote presentation/generate.cjs with Sovereign Intelligence dark theme
- Expanded from 12 slides to 15, adding the missing groundbreaking features
- NEW slide 5: WhatsApp Bot — showstopper with conversation mockup, "500M users" stat
- NEW slide 8: 5-Layer Fraud Detection — image authenticity, GPS, relevance, duplicates, rate limiting
- NEW slide 9: Predictive Maintenance — "fails in 18 days" card + monsoon acceleration factor
- NEW slide 10: Gamification — Pothole Hunter podium visual + 6 feature cards
- NEW slide 11: Wall of Shame + Accountability Loop — contractor negligence ranking
- Upgraded visual design: cards with borders, gradient callouts, colored tag system
- Switched layout from 10x7.5 to widescreen 13.33x7.5
- Color palette: emerald primary, cyan/violet/amber/gold accents, dark cards on black
- Fixed hex+alpha color warnings (now uses transparency property)

## v3.5.2 — Stateful WhatsApp bot: two-step photo + location flow
- Problem: WhatsApp strips EXIF GPS from photos for privacy, so all WhatsApp reports defaulted to Mumbai center coords
- Fix: turned webhook into a two-step conversation
  1. User sends photo → AI detection runs → bot replies with preview and asks for 📎 → Location
  2. User shares WhatsApp location → report finalized with real GPS
- whatsapp_sessions dict now tracks pending_photo per phone number
- Pending photo expires after 15 minutes (prevents stale photo + fresh location mismatch)
- If EXIF GPS is somehow preserved (rare), skips the location-ask and finalizes immediately
- Reply labels the GPS source: "from WhatsApp location share" / "from photo GPS" / "default Mumbai"
- New helper: _finalize_whatsapp_report(phone, reporter, lat, lng, pending, loc_source)
- Refactored welcome + no-damage messages into module constants

## v3.5.1 — Shared file store for cross-process report sync
- Problem: WhatsApp webhook backend (port 8001 HTTP, for ngrok) and frontend backend (port 8000 HTTPS) run as separate processes with isolated in-memory state → WhatsApp reports didn't appear on govt dashboard
- Fix: added shared_store.json file at backend/ that both processes merge with
- Writes (`/public/report`, `/whatsapp/webhook`) call `_persist_shared_store()` after citizen_reports.append
- Reads (`/admin/reports/map`, `/public/reports/map`, `/public/reports/map/detail`) call `_reload_shared_store()` first
- Dedupe by id, latest file state wins on conflict
- shared_store.json added to .gitignore (ephemeral, regenerates on startup)

## v3.5.0 — WhatsApp reporting bot (Twilio Sandbox)
- New endpoint: POST /whatsapp/webhook (Twilio WhatsApp incoming-message webhook)
- Citizens send a photo via WhatsApp to Twilio sandbox number (+1 415 523 8886)
- Backend downloads media from Twilio (auth-protected), extracts GPS from EXIF if present, runs full AI detection pipeline
- Reuses existing detection, severity, cost, gamification logic
- No-damage guard (v3.4.0 threshold) applied — weak detections reply with tips instead of creating a ghost report
- Reply includes: report ID, damage type with severity+confidence, estimated cost, repair method, GPS, XP/coins earned
- Session state tracked per phone number (whatsapp_sessions dict)
- Added dependencies: twilio 9.3, python-dotenv 1.0, httpx 0.27
- Credentials stored in gitignored backend/.env
- .gitignore: added `.env.*` pattern to cover future env variants

## v3.4.0 — Reject reports with no detectable damage
- Backend /public/report: confidence threshold 0.20 → 0.30 (reduces false positives from textures/noise)
- Added early-exit: if no detection ≥ 0.35 confidence, return `status: "no_damage"` without creating a report
- Response includes annotated_image, max_confidence, and tips for better photos
- Public app: new `no_damage` screen with blue friendly alert, annotated preview, confidence score, photography tips, "Try Another Photo" button
- Fixes: clean photos (selfies, cats, plain walls) no longer auto-approved as genuine damage reports

## v3.3.14 — Login page: tone down spacing (v3.3.13 was too spacey)
- Name wrapper marginBottom: 32 → 24
- Password wrapper marginBottom: 56 → 28 (the 56 gap before Get Started looked abnormal)
- Label marginBottom: 14 → 10
- Consistent visual rhythm between fields

## v3.3.13 — Login page: force spacing via inline styles
- Tailwind mb-6/mb-10 utilities were getting overridden or not reflecting visually
- Switched to inline `style={{ marginBottom: 32/56 }}` for guaranteed pixel-exact spacing
- Name field: 32px gap below
- Password field: 56px gap below button
- Labels: 14px gap below to their inputs
- Submit button: py-4 → py-5 (bigger target)
- Error message: 20px margin when visible

## v3.3.12 — Login page: much more spacing (replace space-y with explicit margins)
- space-y-6 was getting overridden by individual mt-* utilities — switched to explicit mb-* on each field
- Gap after Name input: mb-6 (24px)
- Gap after Password input: mb-10 (40px — big breathing room before Get Started)
- Label margin: mb-2.5 → mb-3
- Error message: added mb-4 when visible

## v3.3.11 — Login page: center logo + more padding between fields
- Logo+title wrapper now uses flex flex-col items-center (explicit horizontal centering — mx-auto wasn't working reliably inside the phone frame)
- Form wrapper also centered with flex flex-col items-center
- Fields gap: space-y-4 → space-y-6 (24px instead of 16px between Name and Password)
- Label bottom margin: mb-2 → mb-2.5
- Submit button: added mt-2 to push it off the password field

## v3.3.10 — De-clutter Rewards page (profile + achievements)
- ProfileCard: added shadow-xl for depth, mb-5 → mb-6 between header/XP bar/stats
- Inner stat cards (Day Streak/Badges/Reports): py-3 → py-4, added subtle border
- Achievements section: mt-4 → mt-8 (bigger gap from profile card)
- Achievement grid: gap-2.5 → gap-3, cards p-3.5 → p-4
- Earned achievement cards: added shadow-lg + stronger border

## v3.3.9 — Report page form fills full viewport + bigger buttons
- Post-sector form wrapped in flex-col with min-h-[calc(100dvh-10rem)]
- Photo upload zone grows to fill available space (flex-[2], min-h-[180px])
- Description textarea grows to fill remaining space (flex-1, min-h-[90px])
- Submit button enlarged: py-4 → py-5, text-[14px] → text-[16px], rounded-xl → rounded-2xl
- GPS button: py-3 → py-4, text-[13px] → text-[14px]
- Camera icon in empty state: w-7 → w-8, container w-14 → w-16
- "Tap to take photo" text: [13px] → [14px]
- No more empty white space below Submit Report

## v3.3.8 — Stats page: definitive card separation with shadows
- space-y-8 → space-y-10 (40px gap between every card)
- Added shadow-xl/shadow-lg with shadow-black/40 to every card — clear depth separation
- Card backgrounds bumped white/[0.03] → white/[0.04] for slightly more presence
- Cost card gradient + border made more visible (amber/[0.20] border, amber/[0.08] bg)
- Content scrolls naturally through all sections

## v3.3.7 — Stats page: bigger gaps + visible borders
- space-y-6 → space-y-8 (32px between sections, was 24px)
- Borders doubled in visibility: white/[0.04] → white/[0.08]
- Sections now clearly distinct — no more visual merging

## v3.3.6 — De-clutter Stats page (more breathing room + visual separation)
- Section spacing increased: space-y-4 → space-y-6
- Added subtle borders (border-white/[0.04]) to all cards for clearer visual separation
- Performance ring: size 120 → 110 (easier fit, less visual pressure)
- Footer padding expanded (pt-2/pb-4 → pt-4/pb-6)
- Scroll already worked — spacing was the real problem
- Standardized card rounding: Performance card rounded-xl → rounded-2xl to match Breakdown

## v3.3.5 — Center HOTSPOT logo on Map page
- MapPage header restructured: logo centered horizontally, LIVE badge absolutely positioned top-right
- Was left-aligned and getting clipped by the phone frame's rounded corner

## v3.3.4 — Center all simple page headers in public app
- GamificationPage: "🏆 Pothole Hunter" title + subtitle centered
- StatsPage: "Government Dashboard" title + subtitle centered
- LiveScanPage (sector picker view): "Live Scan" title + subtitle centered
- Complex headers (MapPage, NavigatePage, LiveScanPage scan view) left alone — they contain functional side elements (filters, hazard badges, sector badges)

## v3.3.3 — Center Report page header
- Report Damage title + subtitle now centered (was left-aligned, getting clipped by rounded phone frame)

## v3.3.2 — Hero landing page fits in 100vh (CTAs visible without scroll)
- Root container: min-h-screen → h-screen (prevents overflow/scroll)
- Headline sized down: text-5xl/7xl → text-4xl/5xl/6xl (fits 2 lines instead of 4+)
- Subtitle: text-lg → text-base
- Reduced vertical spacing: badge mb-6→mb-4, headline mb-6→mb-4, subtitle mb-8→mb-6, CTAs mb-10→mb-6
- Nav py-5 → py-4
- "Scroll to explore" → "Tap to enter" (no scroll exists now)
- Launch Dashboard + Watch Demo buttons now visible at first paint on standard viewports

## v3.3.1 — Settings page cleanup: remove non-functional items
- Removed entire "General" section (Dark Mode, Language, Notifications, Default Region — all were click-only placeholders with no behavior)
- Removed fake "API Status: Healthy" row (was hardcoded, not a real health check)
- Kept working items: Profile card, Fraud Detection toggle, AI Model display, Sign Out
- Version label updated v2.0 → v3.3.0
- Cleaned up unused imports (Shield, Bell, MapPin, ChevronRight, Moon, Globe)

## v3.3.0 — Password-protected citizen demo account + auto-seed on startup
- New demo account: username `saud`, password `123` → logs in as "Saud Vinchu" (citizen role)
- Profile pre-loaded: 3200 XP, Lv.6, 180 coins, 45 reports, 21-day streak, 10 badges
- Achievements: first_report, five_reports, ten_reports, twenty_five_reports, streak_3, streak_7, critical_finder, fast_reporter, multi_sector, ai_challenger
- Seeded reports tied to Saud Vinchu: RPT-003 (Mumbai-Pune Expy, 91% severity), RPT-004 (Amity University Rd, fixed), RPT-006 (Old Panvel Bridge, 87% severity)
- Public app CitizenLogin: added optional password field (leave empty for fresh account, fill for demo login)
- Backend startup: auto-seeds gamification + reports demo data — no manual curl needed anymore
- Presentation flow: just enter "saud" + "123" → full demo-ready profile

## v3.2.1 — Unified demo data between govt + public apps
- New backend endpoint: POST /admin/reports/seed-demo — seeds 8 demo citizen reports
- Removed hardcoded DEMO array from public-app/src/pages/MapPage.jsx
- Public app now replaces state with API data (no longer appends to local demos)
- Govt dashboard and public app now show identical report counts from single source of truth
- Seed after backend restart: curl -k -X POST https://HOST:8000/admin/reports/seed-demo

## v3.2.0 — Desktop phone-frame layout + UI polish
- Public PWA now renders inside a centered 420×screen phone frame on desktop (rounded corners, border, shadow); unchanged full-screen on phones
- Report page sector picker: centered vertically, square cards, larger emojis
- Leaderboard podium: unified gold/silver/bronze pillars with flat gradient rank circles (replaces overflowing medal emojis)
- Public app bottom nav: taller bar, no icon-border overlap
- Govt dashboard header: removed search bar + notification bell
- Removed "Full Scan" sector option (Dashboard + New Scan + Video/Live)
- Removed History tab from sidebar + route
- Removed Recent Scans section from Dashboard

## v3.1.1 — Demo data seeding + AI challenge fix
- POST /gamification/seed-demo — seeds 10 demo users with realistic stats
- Saud Vinchu at #1 (3200 XP, Lv.6, 45 reports, 21-day streak, 10 badges)
- Fixed AI challenge endpoint (backend restart required)
- Leaderboard shows proper names, levels, streaks, badge counts

## v3.1.0 — Gamification UI

## v3.0.0 — Full Gamification Engine (12 features)

MASSIVE update — complete citizen engagement + authority accountability system.

1. POTHOLE HUNTER LEADERBOARD
   - Citizens ranked by XP (earned from valid reports)
   - Points: +15 critical, +10 pothole, +7 crack, +5 minor, -5 false
   - GET /gamification/leaderboard

2. DAILY/WEEKLY CHALLENGES
   - "Report 5 potholes today", "Find a critical road", etc.
   - Progress tracking per user
   - GET /gamification/challenges/{user_id}

3. CIVIC COINS SYSTEM
   - Earn coins from reports, verifications, streaks
   - Starting bonus: 50 coins
   - Every report awards coins based on severity

4. COMMUNITY VERIFICATION (Voting)
   - POST /gamification/verify — vote valid/invalid on reports
   - Verifiers earn XP + coins
   - Consensus tracking (valid/invalid/undecided)

5. ACHIEVEMENT SYSTEM (13 badges)
   - 🕵️ First Report, 📸 Scout (5), 🔥 Road Warrior (10)
   - 🛠️ Civic Hero (25), 🌍 City Saver (50)
   - ⚡ Fast Reporter, 🔥 3/7/30-Day Streaks
   - ✅ Verifier, 🚨 Critical Finder, 🔍 Inspector
   - 🤖 AI Master

6. FIX STREAK SYSTEM (Authorities)
   - Track consecutive fixes per area
   - "🔥 On fire!" for 5+ streak
   - GET /gamification/fix-streaks

7. DAMAGE STREAK DETECTION
   - Auto-tracks consecutive reporting days
   - Bonus XP per streak day

8. AI CHALLENGE MODE
   - GET /gamification/ai-challenge — random scenario
   - 4 options, user guesses damage type
   - POST answer → correct/wrong + accuracy tracking

9. LEVEL SYSTEM
   - Level = sqrt(XP / 100) + 1
   - Shown on profile + leaderboard

10. PERSONAL IMPACT DASHBOARD
    - GET /gamification/profile/{user_id}
    - Total reports, coins, streak, achievements, sectors

11. TIME-TO-FIX RACE
    - Authority areas ranked by fix speed
    - Fastest repair times shown

12. AUTO POINTS ON REPORT
    - Every /public/report now returns gamification data
    - Points, XP, coins, new achievements in response

## v2.2.0 — Predictive Maintenance Engine
"This road will fail in X days"

Backend (predictive_engine.py):
- Damage progression rates per type (growth mm/day, worsen %/week)
- Monsoon acceleration (2.5x faster Jun-Sep for India)
- Timeline: predicted severity at 1/2/4/8/12 weeks
- Cost projection: cost now vs delayed 1wk/2wk/1mo/3mo
- Area-wide forecast: which zones will fail next
- Urgency levels: IMMEDIATE/HIGH/MODERATE/LOW

API:
- GET /analytics/forecast — zone risk predictions
- /detect now returns predictions[] with each detection

Frontend:
- New "Predictions" tab in Analytics with area forecast cards
- Risk scores + "Critical failure expected in X days" warnings
- URGENT badge for zones < 30 days to failure
- Each scan detection now shows prediction inline:
  - "Pothole in: X days"
  - "+X%/week severity increase"
  - Urgency badge + risk description

## v2.1.0 — Advanced Analytics: Wall of Shame + Priority Engine + City Health + Before/After
5 major analytics features:

1. WALL OF SHAME (Contractor Accountability):
   - Ranks contractors by performance + negligence score
   - Negligence = severity × days unresolved
   - Worst performer at top (most shameful)
   - Shows fix rate, unfixed count per contractor

2. MAINTENANCE PRIORITY ENGINE:
   - Priority = Severity × Days Unresolved × Community Votes
   - Top 5 most urgent repairs with cost estimates
   - Formula shown for each entry

3. CITY ROAD HEALTH SCORE:
   - 0-100 health rating per city/area
   - Based on: damage density, severity, fix speed
   - Trend indicator: improving/stable/worsening
   - Animated health bars

4. BEFORE vs AFTER TRACKING:
   - POST /analytics/before-after — upload 2 images
   - AI detects damage in both, calculates improvement %
   - Returns annotated images + severity reduction

5. SMART DAMAGE HEATMAP:
   - GET /analytics/heatmap — lat/lng/intensity points
   - Intensity based on severity + fix status

New AdvancedAnalytics component replaces old AnalyticsChart.
Tab selector: Wall of Shame | Fix Priority | City Health

## v2.0.3 — Govt toggle for fraud detection system
- Government Settings page: toggle switch for Fake Report Detection
- Toggle ON: 5-layer fraud check runs on every citizen report
- Toggle OFF: all reports auto-approved without verification
- Green/red status indicator with explanation text
- ShieldCheck/ShieldOff icons
- GET /admin/settings — read current setting
- PATCH /admin/settings — update setting
- Backend skips fraud_detection.run_full_fraud_check when disabled

## v2.0.2 — Show fraud detection results to user
- REJECTED reports: red X icon, trust score, "Why it was rejected" with flag details
- UNDER REVIEW reports: orange warning, trust score, flagged issues, "inspector will review in 24hrs"
- APPROVED reports: now shows "Authenticity Score: XX% ✓"
- Three distinct screens based on backend verdict (auto_approve / flag_for_review / block)
- Backend HTTPS enabled for mixed-content fix on phones

## v2.0.1 — Fix route coloring: safest=green, dangerous=red
- Routes now color-coded by safety score:
  - Green (thick, solid) = SAFEST route
  - Cyan (dashed) = moderate safety
  - Red (dashed) = dangerous route (near potholes)
- Default OSRM route lines hidden (gray) — custom polylines drawn after analysis
- Polylines properly cleaned up on re-route
- Route names: Route A, Route B, Route C

## v2.0.0 — STABLE CHECKPOINT + Smart Navigation Engine
**ISOLATED BRANCH: v2-stable — `git checkout v2-stable` to revert**

Smart navigation with pothole avoidance:
- Pick start/end by typing OR tapping map (📍 buttons)
- Engine finds multiple routes and scores each for safety
- Safety score = 100 - (hazards * 15) - (totalSeverity / 10)
- Route comparison panel: distance, time, hazard count, safety %
- "SAFEST" badge on best route
- Hazard tags show exact potholes along each route
- "Avoid hazards" toggle
- Green line = primary route, cyan dashed = alternatives
- Red circles = unfixed potholes, orange = moderate

## v1.6.2 — Tap-to-report on government map too
- Same quick report feature now on govt Reports Map
- Click anywhere → sector → photo → submit
- Auto-refreshes report list after submission

## v1.6.1 — Tap-to-report from map (debug feature)
- Tap anywhere on the map to drop a pin and report damage at that location
- Quick report bottom sheet: sector picker → photo → description → submit
- Blue pulsing pin shows selected location with coordinates
- Report auto-submits to backend with tapped GPS coordinates
- New pin appears on map instantly after submission
- Works for any location — not limited to user's GPS position

## v1.6.0 — Pothole-aware navigation
- New "Navigate" tab in public app with Google Maps-style routing
- Search from/to locations using OpenStreetMap Nominatim
- Auto-detects user GPS location as start point
- "Avoid Potholes" toggle — shows danger zones on route
- Pothole danger zones visualized (red circles for high severity)
- Route drawn with emerald green line, alternatives in cyan
- Counts hazards along each route
- Bottom legend showing risk levels
- Uses Leaflet Routing Machine (OSRM) for free routing

## v1.5.6 — Fix upvote double-counting on refresh
- Store exact upvote VALUE in localStorage (not increment)
- Prevents 49→51 bug where increment was applied on top of already-incremented data
- Values re-applied when reports array length changes (API load)

## v1.5.5 — Fix upvote count not incrementing
- Upvote counts now stored in localStorage (not just IDs)
- Demo reports (hardcoded) upvotes persist across refresh
- Real API reports upvote via backend + localStorage fallback
- Counts applied on mount so upvotes survive page reload

## v1.5.4 — Skip empty frames in video detection
- Video /detect/video only returns frames where damage was found
- Frames with zero detections are excluded from results
- Reduces response size and clutter in frame viewer

---

## v1.5.3 — CHECKPOINT (Apr 16, 2026)
**STABLE RELEASE — Revert point**
- Fix: Road sector no longer detects corrosion/spalling false positives
- CV disabled for road, spalling-only for building/bridge
- All features tested and working

## v1.5.2 — Sector on video + live feed
- Sector selection added to govt VideoScan and public LiveScanPage
- /detect/video and /detect/frame accept sector param
- Consistent flow: Pick sector → Choose mode → Scan

## v1.5.1 — Sector-first UI flow
- User must pick sector BEFORE upload zone appears (both apps)
- Govt: 5 sector cards with model info
- Public: 2x2 emoji grid
- "Change" button to go back

## v1.5.0 — Sector-based targeted detection
- 5 sectors: Road, Building, Pipeline, Bridge, All
- Each sector runs only relevant AI models
- GET /sectors endpoint lists available options
- Reduces false positives + faster inference

## v1.4.0 — Fake report prevention system
- 5-layer fraud detection on every citizen report
- Image authenticity (moiré, resolution, brightness, edge, color, aspect ratio)
- GPS validation (range, India bounds, spoofing, null island)
- Duplicate detection (50m Haversine radius)
- Rate limiting (10/hour per user)
- Content relevance (AI must find actual damage)
- Combined trust score → auto_approve / flag_for_review / block

## v1.3.1 — Live camera + video on public PWA
- LiveScanPage for citizen app
- Live camera with rear-facing default
- Video upload with frame-by-frame results

## v1.3.0 — Video upload + Live camera feed
- POST /detect/video — frame extraction + per-frame AI
- POST /detect/frame — single base64 frame detection
- Govt VideoScan component with frame viewer + timeline scrubber
- Live camera mode with detection overlay

## v1.2.1 — Upvote fix, report preview, logout
- Upvotes persist in localStorage
- Report submission shows AI-annotated preview + detections + cost
- Logout button on public app

## v1.2.0 — Marker clustering + authenticity check
- react-leaflet-cluster on both maps
- Custom colored dot markers with glow
- Image authenticity check (FFT moiré, resolution, brightness, edges)
- HTTPS SSL certs for camera access

## v1.1.1 — Settings page
- Profile card, system info, AI model name
- Sign Out button with JWT clear

## v1.1.0 — JWT authentication
- POST /auth/login (govt: username+password)
- POST /auth/register (citizen: name only)
- 3 demo govt accounts (admin, inspector, engineer)
- Login pages on both apps
- Session persistence in localStorage

## v1.0.1 — Mobile stats fix
- Tighter padding for phone screens
- Smaller performance ring, compact stat cards

## v1.0.0 — PRODUCTION RELEASE
**Full-stack AI infrastructure damage detection system**
- Dual platform: Govt dashboard + Citizen PWA
- 3-model AI pipeline (YOLOv8s-RDD + CrackSeg + OpenCV)
- 10+ damage types, 757ms inference, fully offline
- Cost estimation in INR with repair plans
- Explainable AI, priority ranking
- Live pothole map with admin controls
- Government transparency dashboard
- Stitch "Sovereign Intelligence" design system

## v0.x — Pre-release development
- dff2a72: Initial HOTSPOT UI (brutalist design)
- 9839d9a: Full-stack AI detection + Roboflow integration
- 87e3ef7: 7 damage types + CV pipeline
- 8ccb546: Command Center (repair plan, cost, explainable AI)
- b380c7f: Zero hardcoded data — all live API
- ae33ad8: Bold premium redesign
- 69d87a6: Stitch design system applied
- 4c4f8e7: Public citizen app + shared backend
- 03ffc0e: Local YOLOv8s model — zero API dependency
- 8fab9c9: Multi-model pipeline (road + building + pipeline)

---

## How to revert
```bash
# Revert to any checkpoint
git checkout v1.5.3

# Create a new branch from checkpoint
git checkout -b fix-branch v1.5.3

# See all tags
git tag -l
```
